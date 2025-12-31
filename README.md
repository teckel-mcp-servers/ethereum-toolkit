# ethereum toolkit
This MCP server provides access to a subset of the teckel web3 Ethereum JSON-RPC services—namely those that require read only access to the blockchain (Mainnet and Sepolia).

The main purpose of the ethereum toolkit is to give LLM/AI/Agents real-time access to Ethereum blockchain basic information. Once connected to an LLM via your MCP-client-of-choice, the LLM can use the suite of tools in the toolkit to provide answers to prompts such as:

_“What is the current Ethereum price in USD on the mainnet and the sepolia testnet?”_

_“What is the current Ethereum gas price on the mainnet and the sepolia testnet?”_

_“What is the balance of my Ethereum Account on the mainnet and the sepolia testnet?”_

_“What transactions have been processed in the latest block on the mainnet and the sepolia testnet?”_

## How to Use the teckel Toolkits

### First download and install the teckel App.
[<img width="120" height="40" alt="Download_on_the_App_Store_Badge_US-UK_RGB_blk_092917" src="https://github.com/user-attachments/assets/ecbe6c7a-02c9-4212-b2ab-58dd90c91bca" />](https://apps.apple.com/gb/app/teckel/id6746805799)
[<img width="135" height="45" alt="GetItOnGooglePlay_Badge_Web_color_English" src="https://github.com/user-attachments/assets/9f9b8443-d759-41c1-9fce-399dc690b439" />](https://play.google.com/store/apps/details?id=io.teckel.app)

### Next generate an API key using the teckel App.

Do the following:

1. Navigate to the Accounts page by tapping on the wallet icon in the upper right corner of the App home screen. Access the API Key Manager for the created or imported Ethereum Account by tapping “Manage API Key” on the Accounts page.

2. Use the API key when making calls to the endpoints. All the available endpoints are available via the teckel App, incorporating the actual API key for the given Ethereum Account.

3. For illustrative purposes, we will use the following fake key d1e12345-c234-45a6-9b76-1234567891ff in the examples presented below. You would substitute your actual API key in place of this fake key.

### Next, decide if you are using the MCP servers or RESTful (API) to access the tools.

# Using the MCP Servers
## Configure your MCP client-of-choice
Whatever the client, the configuration is essentially the same. Namely, provide the client with the MCP server endpoint and access credentials. These are typically in the form of a configuration JSON snippet, using your API key as the “Bearer” token in the “Authorization” tag. For example, here is the precise configuration for use with the Cursor desktop app (navigate within the Cursor app to the Cursor Settings -> Tools & MCP -> + New MCP server and enter these details using your actual teckel API key to replace this fake one).
### MCP JSON configuration (example for use with Cursor)
```
{
 "mcpServers": {    
    "teckel-ethereum-toolkit": {
      "url": "https://mcp-servers.bh.tkllabs.io:9780/ethereum-mcp",
      "headers": {
        "Authorization": "Bearer d1e12345-c234-45a6-9b76-1234567891ff"
      }
    }   
  }
}
```
NOTE: This configuration assumes the HTTP(streamable) protocol. If your client requires the older (now legacy) SSE protocol, replace “ethereum-mcp” with “ethereum-sse”.

### MCP JSON Configuration (example for use with n8n)
Similarly, for use with n8n, below is the JSON code snippet for a sample workflow which accesses the teckel Ethereum and Navigation Toolkits MCP servers. (Save this entire snippet to a .json file; then import the file to your n8n workflow.) You will need to replace the credentials with your own “Bearer Auth” credential within n8n (using your teckel API key as the “Bearer token”). 
```
{
  "name": "Teckel Tools MCP Example",
  "nodes": [
    {
      "parameters": {
        "endpointUrl": "https://mcp-servers.bh.tkllabs.io:9780/ethereum-mcp",
        "authentication": "bearerAuth",
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.mcpClientTool",
      "typeVersion": 1.2,
      "position": [
        -16,
        640
      ],
      "id": "569bf922-b8cb-40a0-beb8-2da890dab925",
      "name": "Teckel Tools MCP server: ethereum",
      "credentials": {
        "httpBearerAuth": {
          "id": "zPHt516IS5uuS2S5",
          "name": "TECKEL APIKEY"
        }
      }
    },
    {
      "parameters": {
        "modelId": {
          "__rl": true,
          "value": "gpt-5",
          "mode": "list",
          "cachedResultName": "GPT-5"
        },
        "messages": {
          "values": [
            {
              "content": "Perform a VFR flight route calculation from EGNS to EGPK, departing 60 minutes from now. Include an intermediate waypoint on magnetic bearing 320 degrees at 20 nm from EGNS, followed by another on true bearing 355 for 15 nm from the previous waypoint.\n"
            }
          ]
        },
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.openAi",
      "typeVersion": 1.8,
      "position": [
        -496,
        368
      ],
      "id": "5c9a94cd-71a4-4d77-84f5-664ef14871af",
      "name": "Message a model",
      "credentials": {
        "openAiApi": {
          "id": "EvoF3L2GoYTtESR3",
          "name": "OpenAi account"
        }
      }
    },
    {
      "parameters": {
        "endpointUrl": "https://mcp-servers.bh.tkllabs.io:9780/navigation-mcp",
        "authentication": "bearerAuth",
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.mcpClientTool",
      "typeVersion": 1.2,
      "position": [
        192,
        640
      ],
      "id": "016dae90-92b4-4eed-9120-f8531bafbd10",
      "name": "Teckel Tools MCP server: navigation",
      "credentials": {
        "httpBearerAuth": {
          "id": "zPHt516IS5uuS2S5",
          "name": "TECKEL APIKEY"
        }
      }
    }
  ],
  "pinData": {},
  "connections": {
    "Teckel Tools MCP server: ethereum": {
      "ai_tool": [
        [
          {
            "node": "Message a model",
            "type": "ai_tool",
            "index": 0
          }
        ]
      ]
    },
    "Teckel Tools MCP server: navigation": {
      "ai_tool": [
        [
          {
            "node": "Message a model",
            "type": "ai_tool",
            "index": 0
          }
        ]
      ]
    }
  },
  "active": false,
  "settings": {
    "executionOrder": "v1"
  },
  "versionId": "626e0784-a43b-4857-bf54-91e7f1c32c59",
  "meta": {
    "templateId": "self-building-ai-agent",
    "templateCredsSetupCompleted": true,
    "instanceId": "3c7702199c1f42229c256ae3782d70f8576bc5028e557d0903826477f97e032b"
  },
  "id": "rAm1XjSle8lYmOcT",
  "tags": []
}
```
# Using the RESTful API Servers
The benefit of using the MCP servers is that the connected LLMs can parse the natural language in the user prompts, populate the required parameters for calling the toolbox API functions, then likewise unravel the returned JSON structure into natural language for presentation to the user. However, if you wish to utilise the teckel toolkits at a lower level (e.g., in your own app), you can call the functions directly using the RESTful (POST) API protocols. You are then responsible for populating the input parameters, and handling the returned JSON output.

The base API URL endpoint is identical to the MCP server endpoint:
```
https://mcp-servers.bh.tkllabs.io:9780/
```
The desired function name must be appended to this URL, and the function arguments must be added as query-string parameters. The teckel API key must be added as a Bearer Token in the Authorization header. The POST protocol must be used (rather than GET), but the body can be empty since the parameters are passed in the query-string.  Below is an example curl request for calling the _perform_sun_calculations_ from the _eth_gasPrice_ method in the Ethereum Toolkit with the required network parameter:
### Curl Request
```
curl -X POST \   'https://mcp-servers.bh.tkllabs.io:9780/eth_gasPrice?network=mainnet' \
  -H 'Authorization: Bearer d1e12345-c234-45a6-9b76-1234567891ff' \
  -H 'Content-Type: application/json'
```
### JSON Response
```
{"gas_price":465912773}
```
## Functions (tools) list
The full suite of functions available in the toolkit can be found at https://teckel.io/ai-mcp-services/

## Pricing
Pricing (via _teckel credits_ purchased within the teckel App) for usage of each function in the toolkit can be found at https://teckel.io/ai-mcp-services/

## Rate Limiting
All MCP/API calls are subject to rate-limiting per API key. The thresholds are not published, but if a given API key has exceeded the rate limit, a 429 error code will be returned on the given call.

## Errors
If the MCP/API call fails due to user error resulting in a 4xx error code, the user is charged one Base Call Fee (see https://teckel.io/ai-mcp-services/). If the API call fails due to server error resulting in a 5xx error code, the user is not charged. If the given call is part of a cascade of nested calls, the above rules apply to the “outer” call: In other words, if the outer call fails with a 4xx error code, the user is charged one Base Call Fee (for the failed outer call), and there will be no charge for any of the cascaded calls (whether they succeeded or not). Likewise, if the outer call fails with a 5xx error code, the user is not charged at all (even if the cascaded calls succeed).
 
## Note on API Key Security
Should you have concerns that your API key has been compromised (and someone else may be consuming your teckel credits by using your key), simply replace the key via the API Key Manager → Replace API key in the teckel App. The old key will then be immediately disabled.

