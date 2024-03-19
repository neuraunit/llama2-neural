
![Logo](https://i.imgur.com/mmJDMEl.png)


# LLAMA-2/CHAT-UI on NPU

This tutorias aim's to show how you can install a llama2 LLM model with chat-ui interface using our NPU engine.


## Appendix

- Preparing the enviroment
- Installing LLAMA-2
- Installing Chat-UI
- Running


## Preparing the Enviroment

First Update the OS system

```bash
sudo apt update
```

Next, install a few prerequisite packages which let apt use packages over HTTPS:

```bash
sudo apt install apt-transport-https ca-certificates curl software-properties-common
```

Then add the GPG key for the official Docker repository to your system:
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

Add the Docker repository to APT sources:

```bash
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
```

This will also update our package database with the Docker packages from the newly added repo.
Make sure you are about to install from the Docker repo instead of the default Ubuntu repo:

```bash
apt-cache policy docker-ce
```

You’ll see output like this, although the version number for Docker may be different:

```txt
docker-ce:
  Installed: (none)
  Candidate: 5:19.03.9~3-0~ubuntu-focal
  Version table:
     5:19.03.9~3-0~ubuntu-focal 500
        500 https://download.docker.com/linux/ubuntu focal/stable amd64 Packages
```

Finally, install Docker:

```bash
sudo apt install docker-ce
```

Docker should now be installed, the daemon started, and the process enabled to start on boot. Check that it’s running:

```bash
sudo systemctl status docker
```

The output should be similar to the following, showing that the service is active and running:
```txt
Output
● docker.service - Docker Application Container Engine
     Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2020-05-19 17:00:41 UTC; 17s ago
TriggeredBy: ● docker.socket
       Docs: https://docs.docker.com
   Main PID: 24321 (dockerd)
      Tasks: 8
     Memory: 46.4M
     CGroup: /system.slice/docker.service
             └─24321 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
```


## Installing LLAMA

Setting necessaries Env Vars
```bash
model=codellama/CodeLlama-7b-Instruct-hf
volume=$PWD/data
```

Starting Docker Container
```bash
docker run --gpus all \
--shm-size 1g \
-p 8080:80 \
-v $volume:/data \
ghcr.io/huggingface/text-generation-inference:latest \
--model-id $model \
--quantize bitsandbytes-nf4 \
--max-input-length 2048 \
--max-total-tokens 4096
```


## Installing CHAT-UI

![Logo](https://miro.medium.com/v2/resize:fit:720/format:webp/0*uutjWWPXg92rqsCz)

### Chat-Ui is the best open-source chat interface used as alternative to chat-gpt and similar and can be used with many LLM models.

Clone The Repo

```bash
git clone https://github.com/huggingface/chat-ui
```

Start a mongo database
```bash
docker run -d -p 27017:27017 --name mongo-chatui mongo:latest
```
**If you wish you can use Mongo Db Atlas Free Tier.

Install nvm and npm
```bash
wget https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh
bash install.sh
```

Restart your terminal to refresh and start using nvm and run the following command.
```bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"
```

Install nvm node and check npm version
```bash
nvm install node
npm --version
```

Install CHAT-UI dependencies.
```bash
cd chat-ui
npm install
```

Create a .env.local file to configure chatui as the following example.
```txt
MONGODB_URL=mongodb://localhost:27017/
PUBLIC_APP_NAME="🦙 Llama Chat UI 💬"
PUBLIC_APP_ASSETS=chatui
PUBLIC_APP_COLOR=green

MODELS=`[
  {
    "name": "meta-llama/Llama-2-7b-chat-hf",
    "datasetName": "meta-llama/Llama-2-7b-chat-hf",
    "description": "Llama 7B Chat",
    "websiteUrl": "https://ai.meta.com/llama/",
    "userMessageToken": "[INST]",
    "assistantMessageToken": "[/INST]",
    "messageEndToken": "</s>",
    "preprompt": "[INST]<<SYS>>\nYou are a helpful, respectful and honest assistant. Always answer as helpfully as possible, while being safe. Your answers should not include any harmful, unethical, racist, sexist, toxic, dangerous, or illegal content. Please ensure that your responses are socially unbiased and positive in nature.\n\nIf a question does not make any sense, or is not factually coherent, explain why instead of answering something not correct. If you don't know the answer to a question, please don't share false information.<</SYS>>\n\n[/INST]",
    "parameters": {
      "temperature": 0.6,
      "top_p": 0.95,
      "repetition_penalty": 1.2,
      "top_k": 50,
      "truncate": 1000,
      "max_new_tokens": 1024
    },
    "preprompt": "Below are a series of dialogues between various people and an AI assistant. The AI tries to be helpful, polite, honest, sophisticated, emotionally aware, and humble-but-knowledgeable. The assistant is happy to help with almost anything, and will do its best to understand exactly what is needed. It also tries to avoid giving false or misleading information, and it caveats when it isn't entirely sure about the right answer. That said, the assistant is practical and really does its best, and doesn't let caution get too much in the way of being useful.\n-----\n",
    "promptExamples": [
      {
        "title": "Write an email from bullet list",
        "prompt": "As a restaurant owner, write a professional email to the supplier to get these products every week: \n\n- Wine (x10)\n- Eggs (x24)\n- Bread (x12)"
      }, {
        "title": "Code a snake game",
        "prompt": "Code a basic snake game in python, give explanations for each step."
      }, {
        "title": "Assist in a task",
        "prompt": "How do I make a delicious lemon cheesecake?"
      }
    ],
    "endpoints": [{
      "url": "http://127.0.0.1:8080"
    }]
  }
]`
```

## Running

Running CHAT-UI

```bash
npm run dev
```

Open the URL in your browser, change YOUR-PUBLIC-IP with you ip sended by the NPU team.
```
http://{YOUR-PUBLIC-IP}:5173/
```

You will see a page like this.

![App Screenshot](https://miro.medium.com/v2/resize:fit:720/format:webp/1*7Y4SHQrZ0JOhIUE2wRVqyA.png)


## Useful Links

 - [chat-ui](https://github.com/huggingface/chat-ui)
 - [chat](https://huggingface.co/chat/)
 - [hugging face](https://huggingface.co/docs/text-generation-inference/index)
 - [bits and bytes](https://github.com/TimDettmers/bitsandbytes)

