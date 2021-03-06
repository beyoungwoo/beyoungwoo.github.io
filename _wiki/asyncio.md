---
layout  : wiki
title   : Python asyncio
summary : Python asyncio
date    : 2018-01-16 10:09:51 +0900
updated : 2018-03-09 14:50:37 +0900
tags    : asyncio
toc     : true
public  : true
parent  : Python
latex   : false
---
* TOC
{:toc}

## 개요
* [asyncio](https://docs.python.org/3/library/asyncio.html){:target="_blank"} (Asynchronous I/O)
* python3.5 이상을 기준으로 작성 (async def, await 사용)
* 비동기 프로그래밍 모듈로써 CPU 작업과 I/O 처리를 병렬로 하게 해줍니다.
* `async def function_name()`으로 native coroutine을 생성해서 처리합니다.
  * Hello world
  
  ```python
	#!/usr/bin/env python3
	import asyncio
	
	async def hello_world():
	    print("Hello World!")
	
	loop = asyncio.get_event_loop()
	# Blocking call which returns when the hello_world() coroutine is done
	loop.run_until_complete(hello_world())
	loop.close()
  ```

* 비동기 처리에 대한 시각적 이해 [출처: 파이썬 코딩도장](https://dojang.io/mod/page/view.php?id=1167){:target="_blank"}
![비동기처리](https://dojang.io/pluginfile.php/5583/mod_page/content/2/048002.png?style=centerimg)

{:.image-caption}
*Figure 1. 비동기 처리 [출처](https://dojang.io/pluginfile.php/5583/mod_page/content/2/048002.png)*{:target="_blank"}

## 예제
* 기본 비동기 처리 예시 ([참고](http://cheat.readthedocs.io/en/latest/python/asyncio.html){:target="_blank"})
  * 기본 main 함수
  이벤트 루프를 생성하고 그 loop를 가지고 처리합니다. `asyncio.get_event_loop()`
  
  ```python
  def main():
      loop = asyncio.get_event_loop()            # 이벤트 루프를 얻음
      loop.run_until_complete(async_main(loop))  # async_main이 끝날 때까지 기다림
      loop.close()                               # 이벤트 루프를 닫음
  
  
  if __name__ == '__main__':
      main()
  ```
  
  * 비동기로 사용할 main 함수
    * 비동기로 사용할 함수를 await 하며 다른 비동기 루틴을 호출하고, 해당 작업이 완료될 떄까지 기다립니다.
	* `post_opinion'이라는 함수를 생성해서 각 언론사별로 오피니언에 대한 정보들을 수집하려 합니다.
	* await 할 함수는 async def로 생성해줍니다.
  
  ```python
  async def async_main(loop):
    await post_opinion(loop)
  ```
  
  * 비동기로 병렬 처리를 할 함수를 호출하고, 결과값을 한번에 가져옵니다.
    * `asyncio.ensure_future`를 호출하고 각 언론사별로 requests.get 수행합니다.
	* 시간이 오래걸리는 호출이므로 비동기로 처리해서 처리 속도를 높입니다.
	* `await asyncio.gather`를 호출해서 받아온 모든 결과값을 한번에 가져옵니다.

  ```python
  async def post_opinion(loop):
    press_list = ['경향신문', '국민일보', '노컷뉴스', '동아일보', '매일경제',
                  '문화일보', '세계신문', '중앙일보', '조선일보', '한겨례',
                  '한국경제', '한국일보']
    futures = [asyncio.ensure_future(fetch_opinion(press, loop)) for press in press_list]
    result = await asyncio.gather(*futures)  # 결과를 한꺼번에 가져옴
  ```
  
  * 병렬 처리르 수행할 함수를 호출합니다.
    * `loop.run_in_executor`를 사용합니다. 저는 executor 사용하지 않아서 None입니다.
	* 2번쨰 인자값은 함수명입니다.
	* 3번째 인자값은 함수의 1번째 인자값이고, 이후의 인자값은 함수의 인자값입니다.

  ```python
  async def fetch_opinion(self, press, loop):
    result = await loop.run_in_executor(None, opinion_news, press)
  ```
  
  * 그동안 느리게 처리되던 함수입니다.
	* 위에 언급한 과정들을 통해서 비동기로 처리되며, 비동기 처리후 처리 속도가 빨라졌습니다.

  ```python
  def opinion_news(press):
     ... 중략 ... 
  ```
