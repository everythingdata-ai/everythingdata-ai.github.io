---
layout: post
title: Data Upskilling - Benjamin Dubreu - Part 1
categories: [Data Engineering, Python, SQL]
---

After miserably failing a coding interview this week, I decided to do a Data Upskilling course that I had purchased a few months ago to polish my skills.
The course is in french by Data Scientist/Engineer [Benjamin Dubreu](https://www.linkedin.com/in/benjamin-dubreu-data/)
You will find here the notes I took from the first 2 chapters about Linux and JOINs in SQL/Python 

## 1 - Linux
### Commands : 
- whoami 
- pwd : current folder - print working directory
- ls : list files in current directory
- cd : change directory / cd .. parent directory / cd - previous directory
- touch day{1..7}
- rm : remove 
- cp file folder : copy
- mv file folder : move (cut)
- && : execute 2 commands (mkdir folder && cd folder)


### VIM : 
- i for insert
- ESC to exit Insert
- :wq or :x to save & exit

### Environment variables : 
- export ENV="dev"
- echo $ENV
- echo $PATH | tr ":" "\n"
- export PATH="/Users/radmou/Desktop:$PATH" : make all executables on the Desktop available from anywhere


### Standard communication streams : 
- STDIN : input - technical name of what the commands we write in the terminal interface
- STDOUT : output - the results of our inputs
- STDERR : error - when a command returns an error
We can redirect these streams : 
ls > /dev/null  or ls 1> /dev/null for STDOUT
ls 2> /dev/null for STDERR


### Utilities :
- history : show history of previously used commands
- ping www.google.com : test your connexion and access to a server
- zip zipfile.zip file_to_zip : compress a file
- man ls : command manual

## 2 - Comprendre les JOINS
### Install ANACONDA
```shell
curl -O https://repo.anaconda.com/archive/Anaconda3-2024.10-1-MacOSX-arm64.sh
bash ~/Anaconda3-2024.10-1-MacOSX-arm64.sh

If you'd prefer that conda's base environment not be activated on startup,
   run the following command when conda is activated:
conda config --set auto_activate_base false
You can undo this by running `conda init --reverse $SHELL`?
```

#### Cross Join

Les cross joins sont très rarement utilisés en pratique. Parce qu'ils sont couteux en termes de ressources. Pourtant, ils sont pratiques pour comprendre intuitivement les autres formes de joins (tous les autres JOINs sont des CROSS JOIN avec des filtres)

Qu'est-ce qu'un cross join ?
C'est le diminutif affectueux pour "produit cartésien".
Je sais, vous n'êtes pas plus avancés, mais un exemple simple va vous aider à rapidement bâtir une intuition autour de ce concept:
Imaginez que vous avez deux "sets": 
- un set qui contient 4 symboles: ♣ ♠ ♥ ♦ 
- un autre set qui contient 9 chiffres et 5 symboles: A, 2, 3, 4, 5, 6, 7, 8, 9, 10, J, Q, K 
Le produit cartésien de ces deux sets revient à faire toutes les paires possibles

De manière générale, on conceptualise le produit cartésien comme un "cross product" :
![image](/images/posts/produit-cartesien.png)


```python
import pandas as pd
import duckdb
import io

csv = '''
beverage,price
orange juice,2.5
Expresso,2
Tea,3
'''
beverages = pd.read_csv(io.StringIO(csv))

csv2 = '''
food_item,food_price
cookie juice,2.5
chocolatine,2
muffin,3
'''
food_items = pd.read_csv(io.StringIO(csv2))

# Cross join using Pandas
output = (
    beverages
    .join(food_items, how="cross")
)
output

# Cross join using SQL
cross_join_query = """
SELECT * FROM beverages
CROSS JOIN food_items
"""
duckdb.sql(cross_join_query).df()
```

A useful tool to visualise Pandas operations :
https://pandastutor.com/vis.html

#### Inner Join
On a démarré par les cross joins parce que ça permet de bâtir de l'intuition sur les autres formes de joins. Ici on va découvrir que tous les autres joins peuvent être imaginés comme des cross_joins avec un filtre.

```sql
import pandas as pd
import duckdb
import io

csv = '''
salary,employee_id
2000,1
2500,2
2200,3
'''

csv2 = '''
employee_id,seniority
1,2ans
2,4ans
'''


salaries = pd.read_csv(io.StringIO(csv))
seniorities = pd.read_csv(io.StringIO(csv2))

cross_join_query_where = """
SELECT salary, salaries.employee_id, seniority 
FROM salaries
CROSS JOIN seniorities
WHERE salaries.employee_id = seniorities.employee_id
"""
duckdb.sql(cross_join_query_where)

cross_join_query_where = """
SELECT salary, salaries.employee_id, seniority 
FROM salaries
INNER JOIN seniorities
ON salaries.employee_id = seniorities.employee_id
"""
duckdb.sql(cross_join_query_where)

# In pandas, use .merge() instead of .join()
salaries.merge(seniorities, on="employee_id", how="inner")
```

Toutes les sortes de joins peuvent être ramenées à un produit cartésien auquel on applique un filtre.

#### Left Join
Les left joins servent à toujours garder la donnée d'origine et venir la compléter.

```python
import pandas as pd
import duckdb
import io

csv = '''
customer_id,product_bought
1234, t-shirt
R2D2, jeans
C3PO, jeans
1234, shoes
R2D2, sweat
C3PO, shorts
'''

csv2 = '''
customer_id,loyalty_card_id
R2D2,887b3c5c-3ff2-11ee-be56-0242ac120002
'''

sales = pd.read_csv(io.StringIO(csv))
loyalty_cards = pd.read_csv(io.StringIO(csv2))

# LEFT JOIN as a CROSS JOIN
cross_join_query_where = """
SELECT product_bought, sales.customer_id, 
CASE WHEN
    loyalty_cards.customer_id != sales.customer_id 
    THEN NULL
    ELSE loyalty_card_id
END AS loyalty_card_id
FROM sales
CROSS JOIN loyalty_cards

"""
duckdb.sql(cross_join_query_where)

# LEFT JOIN
left_join_query_where = """
SELECT * FROM sales
LEFT JOIN loyalty_cards
USING (customer_id) 
"""
duckdb.sql(left_join_query_where)
```



### Full outer join
Un right join ne devrait jamais être utilisé, il suffit d'inverser l'ordre des tables et utiliser un left join.

Quand on souhaite garder à la fois un left ET un right, c'est là qu'intervient le full outer join.

```python
pd.merge(
    stores_and_products,
    df_products,
    on='product_id',
    how='outer'
)

query = """
SELECT * 
FROM stores_and_products
FULL OUTER JOIN df_products
USING (product_id)
"""

duckdb.query(query).df()
```

### Self join
Self Join est différent des joins précédents, c'est tout simplement la jointure d'une table avec elle même, en utilisant l'un des joins menetionnées précédemment.

Le cas de figure le plus intuitif et de trouver l'employé et son supérieur à partir de la table employés (employee_id, supervisor_id) 

```python
import pandas as pd
import duckdb

employees = {
    'employee_id': [11, 12, 13, 14, 15],
    'employee_name': ["Sophie", "Sylvie", "Daniel", "Kaouter", "David"],
    'manager_id': [13, None, 12, 13, 11],
}

df_employees = pd.DataFrame(employees)
df_employees

employee_with_manager = (
    df_employees
    .merge(df_employees,
           left_on="manager_id",
           right_on="employee_id",
           suffixes=["_emp", "_man"],
           how="left"
          )
    # .drop("employee_id_man", axis=1)
)

employee_with_manager

# Autant de fois qu'on veut ;)

(
    employee_with_manager
    .merge(df_employees,
           left_on="manager_id_man",
           right_on="employee_id",
           suffixes=["_n+1", "_n+2"],
           how="left"
          )
    # .drop("employee_id_man", axis=1)
)

# En SQL

query = """
SELECT * FROM 
df_employees le
LEFT JOIN df_employees re
ON le.manager_id = re.employee_id
"""

duckdb.sql(query)
```
