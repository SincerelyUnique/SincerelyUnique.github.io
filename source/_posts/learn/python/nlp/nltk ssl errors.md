---
title: nltk ssl errors
date: 2021-06-18 11:33:26
tags:
categories:
- [学习, Python语言学习, NLP]
---

# nltk ssl errors

## 错误信息(nltk 3.5)

```python
import nltk
 
nltk.download('punkt')    
 [nltk_data] Error loading Punkt: <urlopen error [SSL:
 [nltk_data]     CERTIFICATE_VERIFY_FAILED] certificate verify failed
 [nltk_data]     (_ssl.c:590)>
False
```

## 修改

```python
if __name__ == '__main__':
    import ssl
 
    try:
        _create_unverified_https_context = ssl._create_unverified_context
    except AttributeError:
        pass
    else:
        ssl._create_default_https_context = _create_unverified_https_context
 
    import nltk
    nltk.download('punkt')
    print('end')
```

参考：

https://github.com/gunthercox/ChatterBot/issues/930#issuecomment-322111087
https://stackoverflow.com/questions/38916452/nltk-download-ssl-certificate-verify-failed
