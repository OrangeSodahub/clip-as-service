# Welcome to CLIP-as-service!

```{include} ../README.md
:start-after: <!-- start elevator-pitch -->
:end-before: <!-- end elevator-pitch -->
```

## Try it!

An always-online server `api.clip.jina.ai` loaded with `ViT-L-14-336::openai` is there for you to play & test.
Before you start, make sure you have created an access token from our [console website](https://console.clip.jina.ai/get_started), 
or via CLI as described in [this guide](https://github.com/jina-ai/jina-hubble-sdk#create-a-new-pat).

```bash 
jina auth token create <name of PAT> -e <expiration days>
```

Then, you need to configure the access token in the parameter `credential` of the client in python or set it in the HTTP request header `Authorization` as `<your access token>`.

````{tab} via gRPC ⚡⚡

```bash
pip install clip-client
```

```{code-block} python
---
emphasize-lines: 5
---
from clip_client import Client

c = Client(
    'grpcs://api.clip.jina.ai:2096', 
    credential={'Authorization': '<your access token>'}
)

r = c.encode(
    [
        'First do it',
        'then do it right',
        'then do it better',
        'https://picsum.photos/200',
    ]
)
print(r)
```

````

````{tab} via HTTPS 🔐

```{code-block} bash
---
emphasize-lines: 4
---
curl \
-X POST https://api.clip.jina.ai:8443/post \
-H 'Content-Type: application/json' \
-H 'Authorization: <your access token>' \
-d '{"data":[{"text": "First do it"}, 
    {"text": "then do it right"}, 
    {"text": "then do it better"}, 
    {"uri": "https://picsum.photos/200"}], 
    "execEndpoint":"/"}'
```

````

## Install

![PyPI](https://img.shields.io/pypi/v/clip_client?color=%23ffffff&label=%20) is the latest version.

Make sure you are using Python 3.7+. You can install the client and server independently. It is **not required** to install both: e.g. you can install `clip_server` on a GPU machine and `clip_client` on a local laptop.

````{tab} Client

```bash
pip install clip-client
```

````

````{tab} Server (PyTorch)

```bash
pip install clip-server
```
````

````{tab} Server (ONNX)

```bash
pip install "clip_server[onnx]"
```

````


````{tab} Server (TensorRT)

```bash
pip install nvidia-pyindex 
pip install "clip_server[tensorrt]"
```
````

````{tab} Server on Google Colab

```{button-link} https://colab.research.google.com/github/jina-ai/clip-as-service/blob/main/docs/hosting/cas-on-colab.ipynb
:color: primary
:align: center

{octicon}`link-external` Open the notebook on Google Colab 
```

````


## Quick check

After installing, you can run the following commands for a quick connectivity check.

### Start the server

````{tab} Start PyTorch Server 
```bash
python -m clip_server
```
````

````{tab} Start ONNX Server 
```bash
python -m clip_server onnx-flow.yml
```
````

````{tab} Start TensorRT Server 
```bash
python -m clip_server tensorrt-flow.yml
```
````

At the first time starting the server, it will download the default pretrained model, which may take a while depending on your network speed. Then you will get the address information similar to the following: 

```text
╭────────────── 🔗 Endpoint ───────────────╮
│  🔗     Protocol                   GRPC  │
│  🏠        Local          0.0.0.0:51000  │
│  🔒      Private    192.168.31.62:51000  │
|  🌍       Public   87.105.159.191:51000  |
╰──────────────────────────────────────────╯  
```

This means the server is ready to serve. Note down the three addresses shown above, you will need them later.

### Connect from client

```{tip}
Depending on the location of the client and server. You may use different IP addresses:
- Client and server are on the same machine: use local address, e.g. `0.0.0.0`
- Client and server are connected to the same router: use private network address, e.g. `192.168.3.62`
- Server is in public network: use public network address, e.g. `87.105.159.191`
```

Run the following Python script:

```python
from clip_client import Client

c = Client('grpc://0.0.0.0:51000')
c.profile()
```

will give you:

```text
 Roundtrip  16ms  100%
├──  Client-server network  8ms  49%
└──  Server  8ms  51%
    ├──  Gateway-CLIP network  2ms  25%
    └──  CLIP model  6ms  75%
{'Roundtrip': 15.684750003856607, 'Client-server network': 7.684750003856607, 'Server': 8, 'Gateway-CLIP network': 2, 'CLIP model': 6}
```

It means the client and the server are now connected. Well done!


```{include} ../README.md
:start-after: <!-- start support-pitch -->
:end-before: <!-- end support-pitch -->
```


```{toctree}
:caption: User Guides
:hidden:

user-guides/client
user-guides/server
user-guides/benchmark
user-guides/retriever
user-guides/faq
```

```{toctree}
:caption: Hosting
:hidden:

hosting/by-jina
hosting/on-jcloud
hosting/colab
```

```{toctree}
:caption: Playground
:hidden:

playground/embedding
playground/reasoning
playground/searching
```


```{toctree}
:caption: Developer References
:hidden:
:maxdepth: 1

api/clip_client
changelog/index
```


---
{ref}`genindex` | {ref}`modindex`

