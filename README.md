### Ragna - Amazon Bedrock integration

This is a soft fork of the [Ragna](https://github.com/Quansight/ragna) project that was created to show how you can integrate Amazon Bedrock into this framework. You can find out more by reading [Unboxing Ragna: Getting hands on and making it to work with Amazon Bedrock](https://dev.to/aws/unboxing-ragna-getting-hands-on-and-making-it-to-work-with-amazon-bedrock-7k3)

The code in this repo provides additional code and updates that allow you to use:

* Amazon Bedrock with Anthropic Claude v1
* Amazon Bedrock with Anthropic Claude v2
* Amazon Bedrock with Llama2 13b

Please refer to the original, upstream project (linked above) for documentation and the upstream version of this project. A few things to bear in mind:

* Make sure you enable access to the models within Amazon Bedrock before you try this - if you do not, you will get an error such as "botocore.errorfactory.ResourceNotFoundException: An error occurred (ResourceNotFoundException) when calling the InvokeModel operation: Could not resolve the foundation model from the provided model identifier."
* Currently Llama2 is only available in us-east-1, so bear that in mind when you configure your Amazon Bedrock API access endpoint (BEDROCK_AWS_REGION)

This repo is only intended to showcase how you can integrate Amazon Bedrock, and you should not use this as a replacement of the upstream project.

**Quick Start**

After checking out this repo, use the following commands to get it up and running (for a list of depenedncies check out the blog post above)

```
python --version
Python 3.9.18
git clone https://github.com/094459/ragna.git
conda env create --file environment-dev.yml
conda activate ragna-dev
cd ragna
#pip install 'ragna[all]'
pip install boto3
pip install --editable '.[all]'
```

This will take a few minutes the first time you do this as it downloads all the required libraries and dependencies.

```
ragna --version
ragna 0.1.dev94+ga47fb8c.d20231116114018 from /Users/{username}/Projects/GenAI/oss-ragna/ragna/ragna
```

```
export BEDROCK_AWS_REGION=eu-central-1
cd ragna
ragna init
```

You will see AmazonBedrock options for Anthropic Claude and Llama. This will create your Ragna configuration file (ragna.toml), which should look something like this:

```
local_cache_root = "/Users/{username}/.cache/ragna"

[core]
queue_url = "/Users/{username}/.cache/ragna/queue"
document = "ragna.core.LocalDocument"
source_storages = ["ragna.source_storages.Chroma"]
assistants = ["ragna.assistants.AmazonBedRockClaude", "ragna.assistants.AmazonBedRockClaudev1"]

[api]
url = "http://127.0.0.1:31476"
origins = ["http://127.0.0.1:31477"]
database_url = "sqlite:////Users/{username}/.cache/ragna/ragna.db"
authentication = "ragna.core.RagnaDemoAuthentication"

[ui]
url = "http://127.0.0.1:31477"
origins = ["http://127.0.0.1:31477"]

```

You can check everything is working by running "ragna check" (in the following example, I configure just the Amazon Bedrock Claude providers)

```
┏━━━━┳━━━━━━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━┓
┃    ┃ name                    ┃ environment variables ┃ packages            ┃
┡━━━━╇━━━━━━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━┩
│ ✅ │ Chroma                  │                       │ ✅ chromadb>=0.4.13 │
│    │                         │                       │ ✅ tiktoken         │
├────┼─────────────────────────┼───────────────────────┼─────────────────────┤
│ ✅ │ Ragna/DemoSourceStorage │                       │                     │
├────┼─────────────────────────┼───────────────────────┼─────────────────────┤
│ ✅ │ LanceDB                 │                       │ ✅ chromadb>=0.4.13 │
│    │                         │                       │ ✅ tiktoken         │
│    │                         │                       │ ✅ lancedb>=0.2     │
│    │                         │                       │ ✅ pyarrow          │
└────┴─────────────────────────┴───────────────────────┴─────────────────────┘
                                assistants                                 
┏━━━━┳━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━┓
┃    ┃ name                            ┃ environment variables ┃ packages ┃
┡━━━━╇━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━┩
│ ✅ │ AmazonBedRock/claude-v2         │ ✅ BEDROCK_AWS_REGION │          │
├────┼─────────────────────────────────┼───────────────────────┼──────────┤
│ ✅ │ AmazonBedRock/claude-instant-v1 │ ✅ BEDROCK_AWS_REGION │          │
```

You can then start Ragna with 

```
ragna ui
```

And then open up a browser on http://localhost:31477

> **Tip!** Before starting, run the following command
> ```
> lsof -nP -iTCP -sTCP:LISTEN | grep 31476
>```
> As I encountered that the API service sometimes hangs, and it can cause odd behiour. This command will check to make sure the process is not alive, and if it is, provide you with the PID so you can kill



**Cleaning up**

To remove your environemnt, I used the following command

```
conda deactivate ragna-dev
conda remove --name ragna-dev --all
```
