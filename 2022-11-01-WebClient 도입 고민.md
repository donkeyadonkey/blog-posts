---
aliases: []
categories: []
date-created: November 1, 2022 5:41 PM
Project: Recody
tags: [Spring, 개발 일반]
date-updated: 2023-02-27 06:05
---
<br><br>
## 상황

- 모든 카테고리의 검색 결과를 반환하기 위해 최대 5 가지의 검색 엔드포인트에 요청해야하는 상황.

- 기존 RestTemplate 을 사용하면 blocking 방식이고, 쓰레드를 나눈다고 하면 사용되는 쓰레드가 많아진다.

- RestTemplate 이 deprecated 된다는 말도 있다.
<br><br>
## 걱정

- 스프링의 리액티브 스택에 익숙하지 않다.
<br><br>
## 간단한 사용 후기

- 사용자체는 쉽다.
- 헤더 지정, 파라미터 지정등이 매우 직관적이다.
    - (그동안 구현하고 싶었던 기능들이 이미 깔끔하게 되어있다…미리 써볼걸)

> 기본 url 이 세팅된 WebClient 빈 정의
> 
> - 이때 빈 이름을 지정.

```json
...

		@Bean("MovieServiceWebClient")
		WebClient webClient(){
		    return WebClient.builder()
		                    .baseUrl( baseUrl )
		                    .defaultHeader( "Authorization", "Bearer " + bearerToken )
		                    .build();
		}

...
```

> 주입 받아 사용
> 
> - 빈 이름을 지정하여 주입받아 사용.

```json
...

		private final WebClient movieWebClient;
		private static final String path = "/api/v2/movie/search-query";
		private static final String MOVIE_SEARCH_PARAM_NAME = "movieName";
		private static final String LANGUAGE_PARAM_NAME = "language";
		
		
		public ReactiveCatalogSearchMoviesHandler(
		        @Qualifier("MovieServiceWebClient") WebClient movieWebClient) {
		    this.movieWebClient = movieWebClient;
		}

...
}
```

> 요청 보낼 때 디버그 로그
> 

```json
2022-11-01 18:46:31.301 DEBUG 25824 --- [nio-8140-exec-2] o.s.w.r.f.client.ExchangeFunctions       : [1eb47b74] HTTP GET http://127.0.0.1:8140/api/v2/movie/search-query?movieName=man&language=ko
```