---
layout: post
title: Connecting Claude to the internet
categories: [AI]
---

Today's post will be a short one.

If you're like me, your new best friend is Anthropic's Claude, then you might have seen this text over and over again : "my knowledge has a cutoff date of October 2024"

And if you have been reading my blog, you should know that Anthropic recently introduced MCP : a protocol to help Claude receive information from spcialized servers.

That means that it's now so easy to eonnect Claude to the internet, so let's do that.

First, you need the desktop version of Claude that you can download [here]().

Second, I will be using my favorite Browser Brave for the internet access, through their Search API. 
You need to create an account [here](https://brave.com/search/api/)
By subscribing to the free plan, you can get up to 2,000 queries/month, you will however need to provide your credit card information.

![image](https://github.com/user-attachments/assets/3f6ac28c-2b1a-4dd6-bb7d-8f332fd70692)


Now head over to Claude Desktop and open Settings > Developer.

<img width="394" alt="Screenshot 2025-03-17 at 15 28 54" src="https://github.com/user-attachments/assets/219333d9-4cb8-4fdb-98b5-26af62b0ebc0" />

If you click on Edit Config, Claude will redirect you the the ```claude_desktop_config.json``` file.
Open it with a text editor and paste the code below :

```json
{
  "mcpServers": {
    "brave-search": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-brave-search"],
      "env": {
        "BRAVE_API_KEY": "YOUR_API_KEY_HERE"
      }
    }
  }
}
```

You can get the API key from the API tab on the Brave Search dashboard.

![image](https://github.com/user-attachments/assets/df54f7ba-9da2-4c17-a363-d2df04ba883d)

If you restart your Claude Desktop, you should now see the brave-search MCP : 

<img width="764" alt="image" src="https://github.com/user-attachments/assets/b48078b2-8e53-467d-b918-8858e1f3ae8a" />

Now if you ask Claude for recent information, you won't get the usual response : 

![image](https://github.com/user-attachments/assets/b1b35bf8-0539-4b9d-b7f5-958e9a4a62c1)

Instead Claude will ask for your permission (he knows who's the boss) to use Brave Search :

<img width="755" alt="image" src="https://github.com/user-attachments/assets/3a501202-260f-44a2-bf26-ca40dd1c1a8e" />

And will provide you with a response based on an internet search :

<img width="764" alt="image" src="https://github.com/user-attachments/assets/a59d422a-b1a9-4dbc-9f32-118cf974a461" />





