# PowerShell Open AI API Wrapper
This PowerShell code provides a simple interface to the OpenAI "Chat Completion"` API for the GPT model "gpt-3.5-turbo", allowing users to initiate and continue conversations with the GPT model using PowerShell.

Endpoint used: https://api.openai.com/v1/chat/completions

## Start-ChatGPTforPowerShell 
This function starts a ChatGPT-like conversation with the `gpt-3.5-turbo` model using PowerShell. 

It  prompts the user to continue an existing conversation or start a new one. If the user wants to continue an existing conversation, they must provide the full path to the prompt*.json file. If the user wants to start a new conversation, they must provide the instructor for ChatGPT.

During the conversation, the user can enter their query for ChatGPT. If the user enters 'q' or 'quit', the conversation stops, and the user can export the current prompt for future use. The user can also choose to start a new conversation.

- `$APIKey`: API key for ChatGPT.
- `$model`: The ID of the GPT model to use.
- `$temperature`: The temperature value to use for sampling.
- `$stop`: The string to use as a stopping criterion.
- `$max_tokens`: The maximum number of tokens to generate in the response.

### Import and Export of conversation prompts
A conversation with the gpt-3.5-turbo Model via OpenAI's APIs is made up of prompts. A prompt can also be used to steer the behavour of the AI. It can for instance, become a Pirate, an Actor, or anything you want it to be. Literally. At some point you may want to export your current prompt, so that you can continue on it. 

The import and export functions allow users exactly that; to continue conversations from a previous session or save a conversation for later use.

The user needs to provide the full path to a prompt*.json file that contains the prompts and responses from a previous conversation. The function uses the `Import-PromptFromJson` function to import the prompts and responses from the file.

The export functionality allows the user to save the prompts and responses from the current conversation to a prompt*.json file, which can be used later to continue the conversation. The user is prompted to provide the full path to the file and then it is written to disk using the `Out-File` function.

### Output
This function does not output any values, but displays the conversation in the PowerShell console.

### Usage
```powershell
Start-ChatGPTforPowerShell -APIKey "YOUR_API_KEY" -model "gpt-3.5-turbo" -temperature 0.5 -stop "\n" -max_tokens 600
```


## Invoke-OpenAIAPI
This PowerShell function is used to interact with the OpenAI API to generate text responses for a given prompt using the GPT model. The function takes several parameters to customize the response output. It takes the following Input:

- `$prompt`: The initial prompt to send to the API.
- `$APIKey`: The API key to authenticate the request.
- `$model`: The ID of the GPT model to use.
- `$temperature`: The temperature value to use for sampling.
- `$stop`: The string to use as a stopping criterion.
- `$max_tokens`: The maximum number of tokens to generate in the response.

The function builds a request for the API, converts the prompt and the request body to JSON, and sends the request using Invoke-RestMethod. If the API returns a response, it extracts the text response from the response and returns the new full prompt with the added text response from the API.

### Output
The function returns the updated prompt with the added text response from the API.

### Usage
1. Open a PowerShell console.
2. Load the Invoke-OpenAIAPI function into the console by either copying and pasting the entire function into the console or saving the function to a PowerShell script file and using the Import-Module cmdlet to import the script into the console.
3. Call the function by running the following command:

```powershell
Invoke-OpenAIAPI -prompt "Hello, how can I assist you today?" -APIKey "your-api-key" -model "gpt-3.5-turbo" -temperature 0.7 -stop "\n" -max_tokens 500
```
Make sure to replace the prompt, APIKey, model, temperature, stop, and max_tokens parameters with the appropriate values for your use case.

The function will return a new prompt with the response generated by the OpenAI API appended to the end. The response will also be displayed in the console in green text.


## New-OpenAIAPIPrompt
This function is used to construct a new prompt for sending to the OpenAI API. The prompt can be used as a new conversation or as a continuation of an existing conversation. It takes the following parameters:

- `$query`: The user's query to add to the prompt.
- `$role`: The role to add to the prompt.
- `$instructor`: The instruction string to add to the prompt.
- `$PreviousPrompt`: The previous prompt, if any.

### Output
The function returns a prompt in the form of an array of hash tables, where each hash table represents a message in the conversation. Each hash table has two keys: role, which represents the role of the speaker ("user", "assistant", or "system"), and content, which represents the message text.

### Usage
To create a new prompt:
```powershell
$prompt = New-OpenAIAPIPrompt -query "Hi there!" -role "user" -instructor "You are a helpful AI."
```
## New-OpenAIAPIConversation
A wrapper function that creates the prompt and calls the Open AI API using "New-ChatGPTPrompt" and "Invoke-OpenAIAPI".
- `$query`: The user's query to add to the prompt.
- `$instructor`: The instruction string to add to the prompt.
- `$APIKey`: The API key to authenticate the request.
- `$model`: The ID of the GPT model to use.
- `$temperature`: The temperature value to use for sampling.
- `$stop`: The string to use as a stopping criterion.
- `$max_tokens`: The maximum number of tokens to generate in the response.

### Output
The function returns the output from `Invoke-OpenAIAPI`, which is a string representing the response from the GPT model

### Usage
```powershell
$APIKey = "YOUR_API_KEY"
$model = "gpt-3.5-turbo"
$temperature = 0.5
$stop = "\n"
$max_tokens = 600

$result = New-OpenAIAPIConversation -query "Hello, how are you today?" -instructor "You are a helpful AI." -APIKey $APIKey -model $model -temperature $temperature -stop $stop -max_tokens $max_tokens
```

## Add-OpenAIAPIMessageToConversation
This function adds a user query to an ongoing conversation and generates a response using the OpenAI API. The function takes in the user's query, an array of previous messages in the conversation, an instructor string to add to the prompt, API key for ChatGPT, the ID of the GPT model to use, temperature value to use for sampling, a stopping criterion string, and the maximum number of tokens to generate in the response.

- `$query`: The user's query to add to the prompt.
- `previousMessages: An array of previous messages in the conversation.
- `$instructor`: The instruction string to add to the prompt.
- `$APIKey`: The API key to authenticate the request.
- `$model`: The ID of the GPT model to use.
- `$temperature`: The temperature value to use for sampling.
- `$stop`: The string to use as a stopping criterion.
- `$max_tokens`: The maximum number of tokens to generate in the response.

### Output
The function returns the response generated by the OpenAI API and the updated array of previous messages in the conversation.

### Usage
```powershell
$role = "user"
$query = "What is the capitol of Switzerland?"
$previousMessages = @(
    @{
        role = "system"
        content = "You are a helpful AI".
    },
    @{
        role = "assistant"
        content = "Hello! I'm a GPT-3.5-turbo model. How can I assist you?"
    },
    @{
        role = $role
        content = $query
    }
)

$response, $updatedMessages = Add-OpenAIAPIMessageToConversation -query $query -previousMessages $previousMessages -instructor "Provide laptop recommendations" -APIKey "YOUR_API_KEY" -model "gpt-3.5-turbo" -temperature 0.5 -stop "\n" -max_tokens 600

Write-Host "Bot: $response"
Write-Host "Previous Messages: $updatedMessages"
```

In this example, the function adds a new query to an ongoing conversation where the assistant has introduced itself and generates a response using the OpenAI API. The function returns the response and the updated array of previous messages in the conversation.
