---
title: "JWT Token"
date: 2021-12-15T14:57:22+09:00
categories:
    - Web
tags:
    - Web
    - JWT
    - Token
url: /2021/12/15/jwt-token
featuredImage: /images/jwt-logo.png
images: 
    - /images/jwt-logo.png
published: true
---
# JSON 웹 토큰
JSON 웹 토큰(JSON Web Token, JWT)은 클레임(사용자 정보)을 안전하게 표현하기 위한 인터넷 표준(RFC 7519)이다.  
<br>

## JWT 구조
---
<table>
<tbody><tr>
<th scope="row">헤더</th>
<td style="width: 20em;"><div class="mw-highlight mw-highlight-lang-json mw-content-ltr" dir="ltr"><pre><span></span><span class="p">{</span>
 <span class="nt">&quot;alg&quot;</span> <span class="p">:</span> <span class="s2">&quot;HS256&quot;</span><span class="p">,</span>
 <span class="nt">&quot;typ&quot;</span> <span class="p">:</span> <span class="s2">&quot;JWT&quot;</span>
<span class="p">}</span>
</pre></div>
</td>
<td>서명 생성을 위해 어떤 알고리즘을 사용할지 식별한다.
<p>토큰의 타입을 지정한다.</p>
</td></tr>
<tr>
<th scope="row">페이로드
</th>
<td><div class="mw-highlight mw-highlight-lang-json mw-content-ltr" dir="ltr"><pre><span></span><span class="p">{</span>
 <span class="nt">&quot;loggedInAs&quot;</span> <span class="p">:</span> <span class="s2">&quot;admin&quot;</span><span class="p">,</span>
 <span class="nt">&quot;iat&quot;</span> <span class="p">:</span> <span class="mi">1422779638</span>
<span class="p">}</span>
</pre></div>
</td>
<td><ul><li><code>iss (Issuer)</code>- 발급자</li>
<li><code>sub (Subject)</code>- 주제</li>
<li><code>aud (Audience)</code>- 대상</li>
<li><code>exp (Expiration Time)</code>- 만료 시간</li>
<li><code>nbf (Not Before)</code>- 활성 날짜</li>
<li><code>iat (Issued Time)</code>- 발급 시간</li>
<li><code>jti (JWT ID)</code>- 고유 식별자</li>
</ul>
<p>다음 예는 표준 Issued Time 클레임(<code>iat</code>)과 사용자 지정 클레임(<code>loggedInAs</code>)을 보여준다.
</td></tr>
<tr>
<th scope="row">서명
</th>
<td><div class="mw-highlight mw-highlight-lang-javascript mw-content-ltr" dir="ltr"><pre><span></span><span class="nx">HMAC</span><span class="o">-</span><span class="nx">SHA256</span><span class="p">(</span>
 <span class="nx">base64urlEncoding</span><span class="p">(</span><span class="nx">header</span><span class="p">)</span> <span class="o">+</span> <span class="s1">&#39;.&#39;</span> <span class="o">+</span>
 <span class="nx">base64urlEncoding</span><span class="p">(</span><span class="nx">payload</span><span class="p">)</span><span class="p">,</span>
 <span class="nx">secret</span>
<span class="p">)</span>
</pre></div>
</td>
<td>서명은 <a href="https://ko.wikipedia.org/wiki/%EB%B2%A0%EC%9D%B4%EC%8A%A464" title="베이스64">Base64url 인코딩</a>을 이용하여 헤더와 페이로드를 인코딩하고 이 둘을 점(.) 구분자로 함께 연결시킴으로써 계산된다. 해당 문자열은 그 뒤 헤더에 규정된 암호화 알고리즘(이번 경우에는 HMAC-SHA256)에 유입된다. Base64url 인코딩은 base64와 비슷하지만 각기 다른 영숫자를 사용하며 패딩(padding)은 제외한다.
</td></tr></tbody></table>

<br>
<pre>
<span style="color: #FB015B;">eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9</span>.<span style="color: #D63AFF;">eyJsb2dnZWRJbkFzIjoiYWRtaW4iLCJpYXQiOjE0MjI3Nzk2Mzh9</span>.<span style="color: #00B9F1">qGQsbhR55bCg9Itm9hk_LHayO87LXusqa9_21u1sAwQ</span></pre>

Base64url 인코딩을 적용한 값
<span style="color: #FB015B;">헤더</span>.<span style="color: #D63AFF;">페이로드</span>.<span style="color: #00B9F1;">서명</span> 에 해당된다.  

[JWT Debugger](https://jwt.io/)에 Encoded 값을 위의 값으로 변경하면, 정보를 확인할 수 있다(JWT 토큰을 생성, 검증할 수 있는 디버거 서비스이다).  
<br><br>

## 참조
---
[1] [jwt.io](https://jwt.io/)  
[2] [JSON 웹 토큰 위키백과](https://ko.wikipedia.org/wiki/JSON_%EC%9B%B9_%ED%86%A0%ED%81%B0)