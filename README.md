# Ollama/GraphRag-Git版
參考資料
https://github.com/TheAiSingularity/graphrag-local-ollama

https://github.com/TheAiSingularity/graphrag-local-ollama/issues/51

[[Bug]: Errors in local search · Issue #451 · microsoft/graphrag](https://github.com/microsoft/graphrag/issues/451#issuecomment-2220861232)

https://www.youtube.com/watch?v=J_bDtrl7v30&t=90s

# notion 連結
https://bevel-burrito-3b7.notion.site/Ollama-GraphRag-12a6aa76e1b18034a4bed63dde1cfc0a
### 1.建立conda

```python
conda create -n graphrag-local python=3.10

```

```python
conda activate graphrag-local
```

### 建立目錄


### 安裝ollama (這是python版的)

```python
pip install ollama
```

### 下載模型

請不要用ollama run <model_name>下載

請用ollama pull <model_name>

```python
ollama pull mistral
ollama pull nomic-embed-text
```

### 克隆git

```python
git clone https://github.com/TheAiSingularity/graphrag-local-ollama
```

### 移動到graphrag-local-ollama

graph-local\graphrag-local-ollama

```python
cd graphrag-local-ollama/
```

### 安裝graphrag

```python
pip install -e .
```

### 建立ragtest資料夾

在ragtest裡再建立input資料夾

graph-local\graphrag-local-ollama\ragtest\input

### 複製graphrag-local-ollama裡的input到 ./ragtest/input

### 初始化 ./ragtest 資料夾以建立所需檔案

```python
python -m graphrag.index --init --root ./ragtest
```

如果有出bugModuleNotFoundError: No module named 'past’

請安裝future

```python
pip install future
```

### 複製graphrag-local-ollama裡的settings.yaml取代 ./ragtest/input裡的settings.yaml

### 修改核心內容

llm\openai\openai_embeddings_llm.py 

git內已經改好

```python
        embedding_list = []
        for inp in input:
            embedding = ollama.embeddings(model=self._configuration.model, prompt=inp)
            embedding_list.append(embedding["embedding"])
        return embedding_list
```

query\llm\oai\[embedding.py](http://embedding.py/)新增以下內容

```python
chunk = self.token_encoder.decode(chunk) #修改過
```

```python
# embedding, chunk_len = self._embed_with_retry(chunk, **kwargs)
  embedding = ollama.embeddings(model='nomic-embed-text', prompt=chunk)['embedding']
  chunk_len = len(chunk)
  chunk_embeddings.append(embedding)
  chunk_lens.append(chunk_len)
```


### 建立圖表

```python
python -m graphrag.index --root ./ragtest
```

### global search

```python
python -m graphrag.query --root ./ragtest --method global "What is machinelearning?”
```

```python
python -m graphrag.query --root ./ragtest --method global "What is machinelearning?用中文顯示”
```

採坑紀律

query\llm\oai\ structured_search\ golbal_search\search.py

註解掉

```python
#search_messages = [
#  {"role": "system", "content": search_prompt},
#  {"role": "user", "content": query},
#]
```

替換成

```python
search_messages = [ {"role": "user", "content": search_prompt + "\n\n### USER QUESTION ### \n\n" + query} ]

```

### local search

query\llm\oai\ structured_search\ search_search\search.py

註解掉

```python
#search_messages = [
#  {"role": "system", "content": search_prompt},
#  {"role": "user", "content": query},
#]
```

替換成

```python
search_messages = [ {"role": "user", "content": search_prompt + "\n\n### USER QUESTION ### \n\n" + query} ]

```

額外參考資料

[傻瓜操作：GraphRAG、Ollama 本地部署及踩坑记录_linux中安装配置graphrag+ollama-CSDN博客](https://blog.csdn.net/weixin_42107217/article/details/141649920)

這些我都沒加

tokens = token_encoder.decode(tokens) 

GRAPHRAG_API_KEY=ollama
GRAPHRAG_CLAIM_EXTRACTION_ENABLED=True


