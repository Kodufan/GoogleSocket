# GoogleSocket

Boring license stuff! Google's code is covered under the Apache 2.0 license, meaning that I am obligated to say that the Google Assitant and all appropriate code is property of Google Inc. I have made major modifications to the code to let it work with websockets and run without the need for runtime arguments. The code under this license is free to use for commercial use, however, the code I have written which handles Google's code is under CC BY-NC 4.0 license, meaning you are free to take, modify, build upon, or remix my code as long as credit is provided and the use is non commercial. If you wish to use this project for commercial use, you must take Google's own code and modify it yourself. 

Hello! This is my second major Neos websocket integration project, and I feel like this one is a bit more ambitious. This is, to my knowledge, the first virtual assistant interface inside of Neos, and I am making it open source for anyone to use! Let me tell you how to use it, because it's a bit more complicated...

## Step 0: Install Python

This tool runs entirely on Python. If you don't have it, head over to https://www.python.org/ and grab yourself the latest stable copy.

## Step 1: Set up the Google end

In order for your personalized Google assistant to work, you have to set up a special Google Cloud project with it. 
Go to the [Google Cloud site](https://console.cloud.google.com/cloud-resource-manager) and create a new project. Write down the project ID, you'll need it later. Next, open the [Google Assistant API](https://console.developers.google.com/apis/api/embeddedassistant.googleapis.com/overview) in a new tab. Enable it and add it to the project you just created. Go to your project overview and click "create credentials".

+ For Which API are you using? choose Google Assistant API.
+ For Where will you be calling the API from? choose Other UI (e.g., Windows, CLI tool).
+ For What data will you be accessing? choose User data.
+ Select What credentials do I need? when finished.

Once you finish that, click "Create consent screen". Select application type "public". Type in a name for it, and click "save" on the bottom. Then, click "Create cridentials" and "help me choose". Type in an ID and then click "Create OAuth client ID". Then, click done. Click the download button next to the client ID you just made, and save this somewhere on your PC, preferably a folder on the root of your C:\ drive. Next, open command prompt and enter the commands:

+ py -m pip install google-assistant-sdk[samples]
+ py -m pip install --upgrade google-auth-oauthlib[tool]

This is where it gets a tad tricky... You'll need to find where the google-auth-oauthlib tool was downloaded. If it was like my setup, it should be under %appdata%/local. Once you find it, you'll need to open a command prompt in the directory with the tool and enter:

+ google-oauthlib-tool --client-secrets C:\Where\You\Put\Your\client_id.json --scope https://www.googleapis.com/auth/assistant-sdk-prototype --save --headless

This will begin the process of linking the scripts to your Google account, letting you use the Google assistant. Follow the instructions by opening the url it displays in a browser, logging into the desired account, and copying the string the site gives you back into the command prompt. Allow it the permissions, and you're good...almost. There's one more small step left. Find the googlesamples-assistant-devicetool and open a command prompt in that directory, then type:

+ googlesamples-assistant-devicetool --project-id [Project ID you wrote down earlier] register-model --manufacturer “Assistant SDK developer” --product-name “my-device-identifier” --type LIGHT --model “my-model-identifier”

You should get a message that it was successfully registered. Now you're done with the Google part.

## Step 2: Set up the local end

Here's where the files come into play, you'll need to modify them a little bit depending on what you want to do inside of Neos. There are two main options right now: 

+ the assistant is 100% local, meaning that nobody will hear its response
+ the assistant still only records from your microphone, but others will hear its response

For the first method, you will need to open the file named pushtotalk.py in a python editor. You will need to make these changes:

+ Line 44: Change this directory to the directory you want the buffered .wav files to save to. Make sure to use two backslashes as I did, or else you will get errors.
+ Line 246: Change this to your project ID
+ Line 250 (optional): If you use a different language with your assistant, change it here
+ Line 421: Change the URL to "http://localhost:8000"

Before I describe how to set up the second method, you will need to pick a domain name to use or just use your public IP. Free DNS solutions can be found at https://www.noip.com/. Once you get a domain set up (or decided to not use one), you will need to change line 421 to "http://DNSorIP:8000". An example of using a DNS can be seen with my own personal HTTP server in the code already. You will need to port forward 8000.

Now all your files should be set up and ready to run. All you need to do now is launch the script and http server. Simply double click the pushtotalk.py file to run it, and for the HTTP server, open the recording directory in your command prompt and enter:

+ python3 -m http.server 8000

I've written the code so that old responses are deleted when new responses play, and when you disconnect from the websocket, the entire folder is wiped. This is to prevent people from opening your http server in a web browser and downloading all the assistant's previous responses. Do note that none of your microphone data is ever present in this folder, it only holds the assistant's replies. 

Some things to note: this is a Google assistant that's linked to your personal google account, so asking it questions like "where do I live?" will respond with your home address. Additionally, the assistant may address you by your first name at times. If you don't feel comfortable with the risk of others hearing this, use the first method, as this is 100% local. Even the responses in Neos will only be heard by you, as it uses localhost instead of a publically hosted HTTP server.

## Step 3: Set up the Neos end

This is the easiest step! All you need to do is get the Google assistant button from my public folder. Put your name into the name field, and click "apply". The websocket handles the rest! If everything works correctly, the button should turn green when you press it. Once it's green, the script is using your default mic to record your response. Ask your assistant a question, and await the reply. You can then attach it to your avatar for easy access across worlds, or even hook it up to a physical avatar and drive the visemes with it (it looks hilariously stupid on a nargacuga). If you have any problems at all, shoot me a message on Discord at Kodufan#7558. If there are ways to make this code better, please suggest it, as I had to do a lot of work to get Google's code to work with my websocket. 
