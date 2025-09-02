
<p align="left">
    <a href="README_CN.md">中文</a>&nbsp ｜ English</a>
</p>
<br><br>

<p align="center">
 <img src="https://dscache.tencent-cloud.cn/upload/uploader/hunyuan-64b418fd052c033b228e04bc77bbc4b54fd7f5bc.png" width="400"/> <br>
</p><p></p>


<p align="center">
    🤗&nbsp;<a href="https://huggingface.co/collections/tencent/hunyuan-mt-68b42f76d473f82798882597"><b>Hugging Face</b></a>&nbsp;&nbsp;|&nbsp;&nbsp;
    <img src="https://avatars.githubusercontent.com/u/109945100?s=200&v=4" width="16"/>&nbsp;<a href="https://modelscope.cn/collections/Hunyuan-MT-2ca6b8e1b4934f"><b>ModelScope</b></a>&nbsp;&nbsp;|&nbsp;&nbsp;
</p>

<p align="center">
    🖥️&nbsp;<a href="https://hunyuan.tencent.com" style="color: red;"><b>Official Website</b></a>&nbsp;&nbsp;|&nbsp;&nbsp;
    🕹️&nbsp;<a href="https://hunyuan.tencent.com/chat/HunyuanDefault?from=modelSquare&modelId=hunyuan-mt-7b"><b>Demo</b></a>&nbsp;&nbsp;&nbsp;&nbsp;
</p>

<p align="center">
    <a href="https://github.com/Tencent-Hunyuan/Hunyuan-MT"><b>GITHUB</b></a>
</p>


## Model Introduction

The Hunyuan-MT comprises a translation model, Hunyuan-MT-7B, and an ensemble model, Hunyuan-MT-Chimera. The translation model is used to translate source text into the target language, while the ensemble model integrates multiple translation outputs to produce a higher-quality result. It primarily supports mutual translation among 33 languages, including five ethnic minority languages in China.

## Key Features and Advantages

- In the WMT25 competition, the model achieved first place in 30 out of the 31 language categories it participated in.
- Hunyuan-MT-7B achieves industry-leading performance among models of comparable scale
- Hunyuan-MT-Chimera-7B is the industry’s first open-source translation ensemble model, elevating translation quality to a new level
- A comprehensive training framework for translation models has been proposed, spanning from pretrain → continue pretraining (CPT) → supervised fine-tuning (SFT) → translation rl → ensemble rl, achieving state-of-the-art (SOTA) results for models of similar size

## Related News
* 2025.9.1 We have open-sourced  **Hunyuan-MT-7B** , **Hunyuan-MT-Chimera-7B** on Hugging Face.
<br>


## Performance

<div align='center'>
<img src="imgs/overall_performance.png" width = "80%" />
</div>
You can refer to our technical report for more experimental results and analysis.

<a href="Hunyuan_MT_Technical_Report.pdf"><b>Technical Report</b> </a>

&nbsp;

## Model Links
| Model Name  | Description | Download |
| ----------- | ----------- |-----------
| Hunyuan-MT-7B  | Hunyuan 7B translation model |🤗 [Model](https://huggingface.co/tencent/Hunyuan-MT-7B)|
| Hunyuan-MT-7B-fp8 | Hunyuan 7B translation model，fp8 quant    | 🤗 [Model](https://huggingface.co/tencent/Hunyuan-MT-7B-fp8)|
| Hunyuan-MT-Chimera | Hunyuan 7B translation ensemble model    | 🤗 [Model](https://huggingface.co/tencent/Hunyuan-MT-Chimera-7B)|
| Hunyuan-MT-Chimera-fp8 | Hunyuan 7B translation ensemble model，fp8 quant     | 🤗 [Model](https://huggingface.co/tencent/Hunyuan-MT-Chimera-7B-fp8)|

## Prompts

### Prompt Template for ZH<=>XX Translation.
```
把下面的文本翻译成<target_language>，不要额外解释。

<source_text>
```


### Prompt Template for XX<=>XX Translation, excluding ZH<=>XX.
```
Translate the following segment into <target_language>, without additional explanation.

<source_text>
```

### Prompt Template for Hunyuan-MT-Chimera-7B

```
Analyze the following multiple <target_language> translations of the <source_language> segment surrounded in triple backticks and generate a single refined <target_language> translation. Only output the refined translation, do not explain.

The <source_language> segment:
```<source_text>```

The multiple `<target_language>` translations:
1. ```<translated_text1>```
2. ```<translated_text2>```
3. ```<translated_text3>```
4. ```<translated_text4>```
5. ```<translated_text5>```
6. ```<translated_text6>```
```

&nbsp;

### Use with transformers
First, please install transformers, recommends v4.56.0
```SHELL
pip install transformers==4.56.0
```

*!!! If you want to load fp8 model with transformers, you need to change the name"ignored_layers" in config.json to "ignore" and upgrade the compressed-tensors to compressed-tensors-0.11.0.*

The following code snippet shows how to use the transformers library to load and apply the model.

we use tencent/Hunyuan-MT-7B for example

```python
from transformers import AutoModelForCausalLM, AutoTokenizer
import os

model_name_or_path = "tencent/Hunyuan-MT-7B"

tokenizer = AutoTokenizer.from_pretrained(model_name_or_path)
model = AutoModelForCausalLM.from_pretrained(model_name_or_path, device_map="auto")  # You may want to use bfloat16 and/or move to GPU here
messages = [
    {"role": "user", "content": "Translate the following segment into Chinese, without additional explanation.\n\nIt’s on the house."},
]
tokenized_chat = tokenizer.apply_chat_template(
    messages,
    tokenize=True
    add_generation_prompt=False,
    return_tensors="pt"
)

outputs = model.generate(tokenized_chat.to(model.device), max_new_tokens=2048)
output_text = tokenizer.decode(outputs[0])
```

We recommend using the following set of parameters for inference. Note that our model does not have the default system_prompt.

```json
{
  "top_k": 20,
  "top_p": 0.6,
  "repetition_penalty": 1.05,
  "temperature": 0.7
}
```

&nbsp;

Supported languages:
| Languages         | Abbr.   | Chinese Names   |
|-------------------|---------|-----------------|
| Chinese           | zh      | 中文            |
| English           | en      | 英语            |
| French            | fr      | 法语            |
| Portuguese        | pt      | 葡萄牙语        |
| Spanish           | es      | 西班牙语        |
| Japanese          | ja      | 日语            |
| Turkish           | tr      | 土耳其语        |
| Russian           | ru      | 俄语            |
| Arabic            | ar      | 阿拉伯语        |
| Korean            | ko      | 韩语            |
| Thai              | th      | 泰语            |
| Italian           | it      | 意大利语        |
| German            | de      | 德语            |
| Vietnamese        | vi      | 越南语          |
| Malay             | ms      | 马来语          |
| Indonesian        | id      | 印尼语          |
| Filipino          | tl      | 菲律宾语        |
| Hindi             | hi      | 印地语          |
| Traditional Chinese | zh-Hant| 繁体中文        |
| Polish            | pl      | 波兰语          |
| Czech             | cs      | 捷克语          |
| Dutch             | nl      | 荷兰语          |
| Khmer             | km      | 高棉语          |
| Burmese           | my      | 缅甸语          |
| Persian           | fa      | 波斯语          |
| Gujarati          | gu      | 古吉拉特语      |
| Urdu              | ur      | 乌尔都语        |
| Telugu            | te      | 泰卢固语        |
| Marathi           | mr      | 马拉地语        |
| Hebrew            | he      | 希伯来语        |
| Bengali           | bn      | 孟加拉语        |
| Tamil             | ta      | 泰米尔语        |
| Ukrainian         | uk      | 乌克兰语        |
| Tibetan           | bo      | 藏语            |
| Kazakh            | kk      | 哈萨克语        |
| Mongolian         | mn      | 蒙古语          |
| Uyghur            | ug      | 维吾尔语        |
| Cantonese         | yue     | 粤语            |


### Training Data Format

If you need to fine-tune our Instruct model, we recommend processing the data into the following format.

```python

messages = [
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "Why is seawater salty?" },
    {"role": "assistant", "content": "Seawater is primarily saline due to dissolved salts and minerals. These substances come from the chemical materials in rocks and soil on the Earth's surface, which are carried into the ocean over time. When seawater evaporates, the water vapor leaves, but the salts and minerals remain, making the seawater saltier. Therefore, the salinity of seawater is determined by the amount of salts and minerals it contains."}
]

from transformers import AutoTokenizer
tokenizer = AutoTokenizer.from_pretrained("your_tokenizer_path", trust_remote_code=True)
train_ids = tokenizer.apply_chat_template(messages)
```

&nbsp;

### Train with LLaMA-Factory

In the following chapter, we will introduce how to use `LLaMA-Factory` to fine-tune the `Hunyuan` model.

#### Prerequisites

Verify installation of the following dependencies:
- **LLaMA-Factory**: Follow [official installation guide](https://github.com/hiyouga/LLaMA-Factory)
- **DeepSpeed** (optional): Follow [official installation guide](https://github.com/deepspeedai/DeepSpeed#installation)
- **Transformer Library**: Use the companion branch (Hunyuan-submitted code is pending review)
    ```
    pip install git+https://github.com/huggingface/transformers@4970b23cedaf745f963779b4eae68da281e8c6ca
    ```

#### Data preparation

We need to prepare a custom dataset:
1. Organize your data in `json` format and place it in the `data` directory in `LLaMA-Factory`. The current implementation uses the `sharegpt` dataset format, which requires the following structure:
```
[
  {
    "messages": [
      {
        "role": "system",
        "content": "System prompt (optional)"
      },
      {
        "role": "user",
        "content": "Human instruction"
      },
      {
        "role": "assistant",
        "content": "Model response"
      }
    ]
  }
]
```
Refer to the [Data Format](#training-data-format) section mentioned earlier for details.

2. Define your dataset in the data/dataset_info.json file using the following format:
```
"dataset_name": {
  "file_name": "dataset.json",
  "formatting": "sharegpt",
  "columns": {
    "messages": "messages"
  },
  "tags": {
    "role_tag": "role",
    "content_tag": "content",
    "user_tag": "user",
    "assistant_tag": "assistant",
    "system_tag": "system"
  }
}
```

#### Training execution

1. Copy all files from the `llama_factory_support/example_configs` directory to the `example/hunyuan` directory in `LLaMA-Factory`.
2. Modify the model path and dataset name in the configuration file `hunyuan_full.yaml`. Adjust other configurations as needed:
```
### model
model_name_or_path: [!!!add the model path here!!!]

### dataset
dataset: [!!!add the dataset name here!!!]
```
3. Execute training commands:
    *​​Single-node training​​
    Note: Set the environment variable DISABLE_VERSION_CHECK to 1 to avoid version conflicts.
    ```
    export DISABLE_VERSION_CHECK=1
    llamafactory-cli train examples/hunyuan/hunyuan_full.yaml
    ```
    *Multi-node training​​
    Execute the following command on each node. Configure NNODES, NODE_RANK, MASTER_ADDR, and MASTER_PORT according to your environment:
    ```
    export DISABLE_VERSION_CHECK=1
    FORCE_TORCHRUN=1 NNODES=${NNODES} NODE_RANK=${NODE_RANK} MASTER_ADDR=${MASTER_ADDR} MASTER_PORT=${MASTER_PORT} \
    llamafactory-cli train examples/hunyuan/hunyuan_full.yaml
    ```

&nbsp;


## Quantization Compression
We used our own [AngleSlim](https://github.com/tencent/AngelSlim) compression tool to produce FP8 and INT4 quantization models. `AngleSlim` is a toolset dedicated to creating a more user-friendly, comprehensive and efficient model compression solution.

### FP8 Quantization
We use FP8-static quantization, FP8 quantization adopts 8-bit floating point format, through a small amount of calibration data (without training) to pre-determine the quantization scale, the model weights and activation values will be converted to FP8 format, to improve the inference efficiency and reduce the deployment threshold. We you can use AngleSlim quantization, you can also directly download our quantization completed open source model to use [AngelSlim](https://huggingface.co/AngelSlim).


## Deployment

For deployment, you can use frameworks such as **TensorRT-LLM**, **vLLM**, or **SGLang** to serve the model and create an OpenAI-compatible API endpoint.

image: https://hub.docker.com/r/hunyuaninfer/hunyuan-7B/tags


### TensorRT-LLM

#### Docker Image

We provide a pre-built Docker image based on the latest version of TensorRT-LLM.

We use tencent/Hunyuan-7B-Instruct for example
- To get started:

```
docker pull docker.cnb.cool/tencent/hunyuan/hunyuan-7b:hunyuan-7b-trtllm
```
```
docker run --privileged --user root --name hunyuanLLM_infer --rm -it --ipc=host --ulimit memlock=-1 --ulimit stack=67108864 --gpus=all hunyuaninfer/hunyuan-7B:hunyuan-7b-trtllm
```

- Prepare Configuration file:

```
cat >/path/to/extra-llm-api-config.yml <<EOF
use_cuda_graph: true
cuda_graph_padding_enabled: true
cuda_graph_batch_sizes:
- 1
- 2
- 4
- 8
- 16
- 32
print_iter_log: true
EOF
```


- Start the API server:


```
trtllm-serve \
  /path/to/HunYuan-7b \
  --host localhost \
  --port 8000 \
  --backend pytorch \
  --max_batch_size 32 \
  --max_num_tokens 16384 \
  --tp_size 2 \
  --kv_cache_free_gpu_memory_fraction 0.6 \
  --trust_remote_code \
  --extra_llm_api_options /path/to/extra-llm-api-config.yml
```


### vllm

#### Start
Please use vLLM version v0.10.0 or higher for inference.

First, please install transformers. We will merge it into the main branch later.
```SHELL
pip install git+https://github.com/huggingface/transformers@4970b23cedaf745f963779b4eae68da281e8c6ca
```

We use tencent/Hunyuan-7B-Instruct for example
- Download Model file:
  - Huggingface:  will download automicly by vllm.
  - ModelScope: `modelscope download --model Tencent-Hunyuan/Hunyuan-7B-Instruct`

- model download by huggingface:
```shell
export MODEL_PATH=tencent/Hunyuan-7B-Instruct
```

- model downloaded by modelscope:
```shell
export MODEL_PATH=/root/.cache/modelscope/hub/models/Tencent-Hunyuan/Hunyuan-7B-Instruct/
```

- Start the API server:

```shell
python3 -m vllm.entrypoints.openai.api_server \
    --host 0.0.0.0 \
    --port 8000 \
    --trust-remote-code \
    --model ${MODEL_PATH} \
    --tensor-parallel-size 1 \
    --dtype bfloat16 \
    --quantization experts_int8 \
    --served-model-name hunyuan \
    2>&1 | tee log_server.txt
```
- After running service script successfully, run the request script
```shell
curl http://0.0.0.0:8000/v1/chat/completions -H 'Content-Type: application/json' -d '{
"model": "hunyuan",
"messages": [
    {
        "role": "system",
        "content": [{"type": "text", "text": "You are a helpful assistant."}]
    },
    {
        "role": "user",
        "content": [{"type": "text", "text": "请按面积大小对四大洋进行排序，并给出面积最小的洋是哪一个？直接输出结果。"}]
    }
],
"max_tokens": 2048,
"temperature":0.7,
"top_p": 0.6,
"top_k": 20,
"repetition_penalty": 1.05,
"stop_token_ids": [127960]
}'
```
#### Quantitative model deployment
This section describes the process of deploying a post-quantization model using vLLM.

Default server in BF16.

##### Int8 quantitative model deployment
Deploying the Int8-weight-only version of the HunYuan-7B model only requires setting the environment variables

Next we start the Int8 service. Run:
```shell
python3 -m vllm.entrypoints.openai.api_server \
    --host 0.0.0.0 \
    --port 8000 \
    --trust-remote-code \
    --model ${MODEL_PATH} \
    --tensor-parallel-size 1 \
    --dtype bfloat16 \
    --served-model-name hunyuan \
    --quantization experts_int8 \
    2>&1 | tee log_server.txt
```


##### Int4 quantitative model deployment
Deploying the Int4-weight-only version of the HunYuan-7B model only requires setting the environment variables , using the GPTQ method
```shell
export MODEL_PATH=PATH_TO_INT4_MODEL
```
Next we start the Int4 service. Run
```shell
python3 -m vllm.entrypoints.openai.api_server \
    --host 0.0.0.0 \
    --port 8000 \
    --trust-remote-code \
    --model ${MODEL_PATH} \
    --tensor-parallel-size 1 \
    --dtype bfloat16 \
    --served-model-name hunyuan \
    --quantization gptq_marlin \
    2>&1 | tee log_server.txt
```

##### FP8 quantitative model deployment
Deploying the W8A8C8 version of the HunYuan-7B model only requires setting the environment variables


Next we start the FP8 service. Run
```shell
python3 -m vllm.entrypoints.openai.api_server \
    --host 0.0.0.0 \
    --port 8000 \
    --trust-remote-code \
    --model ${MODEL_PATH} \
    --tensor-parallel-size 1 \
    --dtype bfloat16 \
    --served-model-name hunyuan \
    --kv-cache-dtype fp8 \
    2>&1 | tee log_server.txt
```




### SGLang

#### Docker Image

We also provide a pre-built Docker image based on the latest version of SGLang.

We use tencent/Hunyuan-7B-Instruct for example

To get started:

- Pull the Docker image

```
docker pull lmsysorg/sglang:latest
```

- Start the API server:

```
docker run --entrypoint="python3" --gpus all \
    --shm-size 32g \
    -p 30000:30000 \
    --ulimit nproc=10000 \
    --privileged \
    --ipc=host \
     lmsysorg/sglang:latest \
    -m sglang.launch_server --model-path hunyuan/huanyuan_7B --tp 4 --trust-remote-code --host 0.0.0.0 --port 30000
```

Citing Hunyuan-MT:

```bibtex
@misc{hunyuanmt2025,
  title={Hunyuan-MT Technical Report},
  author={Mao Zheng, Zheng Li, Bingxin Qu, Mingyang Song, Yang Du, Mingrui Sun, Di Wang, Tao Chen, Jiaqi Zhu, Xingwu Sun, Yufei Wang, Can Xu, Chen Li, Kai Wang, Decheng Wu},
  howpublished={\url{https://github.com/Tencent-Hunyuan/Hunyuan-MT}},
  year={2025}
}
```

## Contact Us

If you would like to leave a message for our R&D and product teams, Welcome to contact our open-source team . You can also contact us via email (hunyuan_opensource@tencent.com).
