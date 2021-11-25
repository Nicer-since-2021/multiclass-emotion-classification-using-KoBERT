# 코드 간단 설명

감정을 다중 분류할 수 있는 코드이다.

코드에 대한 자세한 설명은 https://hoit1302.tistory.com/159 에서 확인해볼 수 있다.

SOOMIN-KIM 폴더의 파일과는 model과 tokenizer를 불러오는 방식이 다르다.

이렇게 수행해본 이유는 다른 모델에 대해서도 적용해보고 싶었기 때문인데 

결국 추가적으로 KcBERT로 실행시키는 것을 실패했고, 어디까지 이해했고 무엇을 도전해보았는지에 기술해놓았다.

(KcBERT 관련 코드 업로드 X)

## SOOMIN-KIM dir과 다른 점

수민이의 폴더에 있는 파일과 다른 점은 아래와 같다.

```python
from transformers import AutoTokenizer, AutoModel 
  
tokenizer = AutoTokenizer.from_pretrained("skt/kobert-base-v1")
bertmodel = AutoModel.from_pretrained("skt/kobert-base-v1")

# print(tokenizer.vocab_file)

 # 해당 부분 코드로 vocab를 불러올 수 있습니다 !!!
vocab = nlp.vocab.BERTVocab.from_sentencepiece(tokenizer.vocab_file, padding_token='[PAD]')
```

바로 model과 tokenizer를 불러오는 방식이 다르다.

따라서 vocab를 불러오는 방식도 다르다.

## 어떻게 다른가?

![image](https://user-images.githubusercontent.com/68107000/143493818-fe216323-a32e-46b2-9eeb-45170f4b7e8a.png)

git에서 다운받는 형식이 아니라, 

huggingface에 올려둔 모델을 transformers를 통해 불러오는 방식이다.

웹사이트 링크: [#](https://huggingface.co/skt/kobert-base-v1/tree/main)

그리고 이 코드에서 중요한 점은 vocab를 `gluonnlp.vocab.BERTVocab` class에 맞는 형식으로 불러와야한다.

BERTVocab api 링크: [#](https://nlp.gluon.ai/api/modules/vocab.html#gluonnlp.vocab.BERTVocab)

그래야 코드 아래에 나오는 `BERTDataset` class를 init할 때 `nlp.data.BERTSentenceTransform` method를 잘 실행시킬 수 있다.

## 이걸 수행한 이유 - KcBERT

이렇게 AutoTokenizer와 AutoModel로부터 불러올 수 있도록 코드를 고친 이유는 AutoTokenizer와 AutoModel에서 지원하는 **다른 BERT 모델에 대해서도 정확도 검증**을 시행해보고 싶었기 때문이다.

수행해보고 싶었던 모델은 대표적으로 [KcBERT](https://github.com/Beomi/KcBERT), [KcELECTRA](https://github.com/Beomi/KcELECTRA) 가 있었다.

하지만 잘 실행될 것이라는 내 예상과는 다르게 vocab를 불러오는 과정에서 막혔다.

### KoBERT는... (original)

![image](https://user-images.githubusercontent.com/68107000/143494405-820dd3f5-6cef-435a-ab22-b49ab38e2884.png)

**KoBERT**에서는 위와 같이 tokenizer.vocab_file에 값이 있어 쉽게 불러올 수 있었다.

( 이코드에 대한 idea는 여기서 확인해볼 수 있었다. 링크: [#](https://github.com/SKTBrain/KoBERT/blob/8df69ec6b588ae661bef98d28ec29448482bbe6e/kobert/pytorch_kobert.py#L60))

### KcBERT... (new!)

![image](https://user-images.githubusercontent.com/68107000/143495779-89f2cc0a-f17e-4b7a-a4c0-baf55c60b79e.png)

**KcBERT**도 KoBERT의 tokenizer와 같은 API를 사용하고 있는 것을 확인했지만 (`transformers.BertTokenizerFast` api 링크: [#](https://huggingface.co/transformers/model_doc/bert.html#berttokenizerfast)) 구현할 때 `tokenizer.vocab_file` attribute의 값은 써두지 않도록 구현해두신 것 같다.

그래서 다시 `gluonnlp.vocab.BERTVocab`의 object를 생성시켜주는 메서드를 확인해보고, 

tokenizer의 api 문서에서 어떤 attribute가 있는지 열심히 찾아보았지만 해답을 찾지 못한 상태이다.

## conclusion

아직 지식이 많이 짧아 공식 api 문서 만으로 코딩하는 것에는 큰 어려움이 있어 다른 모델에 대한 검증을 시도해보는 것은 여기까지 마무리하려고 한다.

