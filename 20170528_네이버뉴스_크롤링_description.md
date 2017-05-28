
<html>
<head>
</head>
<style>

</style>
<body>
<h1 style="line-height: 1.8;"><span style="font-family: NanumGothic;">네이버 뉴스 크롤링</span></h1>



<p style="text-align: right"><span style ="font-family: Tahoma;">Last update: May 28. 2017<br>Kyusik Kim</span></p>

</body>
</html>


* 깔끔하지도 않고 오직 나만을 위해 만든 크롤러이기 때문에 몇 가지 TIP을 공유하는 차원에서 글을 쓴다. (공유라기 보다는 그냥 내가 까먹지 않으려고... 일을 그만 두면서 코드를 가지고 왔다고 생각했는데, 감쪽같이 사라져 새로 만드는 데 난감했기 때문이다)
* 먼저 크롤링(crawling)은 우리가 인터넷 서핑할 때 보는 웹 페이지 내에 있는 데이터를 추출해 내는 행위를 말하며, 이를 수행하는 일련의 시스템(?)을 크롤러(crawler)라 할 수 있겠다. 다양한 방법이 있지만, 나는 Python을 이용한다. 
* Python을 이용해 크롤링을 할 때 핵심이 되는 라이브러리를 열거하면, BeautifulSoup, requests, urllib.request 등이 있다. 보조적으로, pandas, re, time, random 을 이용한다.  
![그림1](https://github.com/iamglenn/mypython/blob/master/navernews/pic/pic_01.png?raw=true)


* 네이버 뉴스의 경우 기사 제목인 "포천시~ 참가" 부분과 빨간색 네모박스의 "국제뉴스" 부분의 url이 다르다. 둘의 차이를 설명하면, 기사 제목을 누르면 국제뉴스와 연결되고, 국제뉴스를 누르면 네이버뉴스 플랫폼에서 제공하는 국제뉴스의 기사에 연결된다는 점이 다르다. 이런 것은 좀 더 편하게 크롤링을 하기 위한 꼼수라고 해야 할까?
* 순서는 아래의 내용을 참고

* **import module**


```python
from bs4 import BeautifulSoup
import requests
import pandas as pd
from pandas import Series, DataFrame
import urllib.request
import re
import time
import random
```

* **네이버 뉴스 url 입력하기**


```python
url_basic = "http://news.naver.com/main/search/search.nhn?query=%BE%C6%B4%C2%C7%FC%B4%D4&st=news.all&q_enc=EUC-KR&r_enc=UTF-8&r_format=xml&rp=none&sm=all.basic&ic=all&so=rel.dsc&rcnews=exist:032:005:086:020:021:081:022:023:025:028:038:469:&rcsection=exist:&stDate=range:20170521:20170528&detail=0&pd=3&r_cluster2_start=11&r_cluster2_display=10&start=11&display=10&startDate=2017-05-21&endDate=2017-05-28&page=1"
url_basic = url_basic[:-1]
```

* **총 페이지 수**


```python
total_pages = 1
```

* **내보낼 때 파일 이름**


```python
file_name = r'\아는형님'
```


```python
article_url = []
press_name = []
press_time = []
press_title = []
press_text = []
```

* **url 가져오기**


```python
url = url_basic + str(1)
print(url)
```

    http://news.naver.com/main/search/search.nhn?query=%BE%C6%B4%C2%C7%FC%B4%D4&st=news.all&q_enc=EUC-KR&r_enc=UTF-8&r_format=xml&rp=none&sm=all.basic&ic=all&so=rel.dsc&rcnews=exist:032:005:086:020:021:081:022:023:025:028:038:469:&rcsection=exist:&stDate=range:20170521:20170528&detail=0&pd=3&r_cluster2_start=11&r_cluster2_display=10&start=11&display=10&startDate=2017-05-21&endDate=2017-05-28&page=1
    


```python
source_code_from_URL = urllib.request.urlopen(url)
print(source_code_from_URL)
```

    <http.client.HTTPResponse object at 0x000002210DD11DA0>
    

* **BeautifulSoup으로 가져오기**


```python
soup = BeautifulSoup(source_code_from_URL, "lxml", from_encoding = "utf-8")
print(soup)
```

    <!DOCTYPE HTML>
    <html lang="ko">
    <head>
    <meta charset="utf-8"/>
    <meta content="IE=edge" http-equiv="X-UA-Compatible"/>
    <meta content="width=1023" name="viewport"/>
    <meta content="뉴스검색 : 네이버 뉴스" property="og:title"/>
    <meta content="website" property="og:type"/>
    <meta content="http://news.naver.com/main/search/search.nhn" property="og:url"/>
    <meta content="http://static.news.naver.net/image/news/ogtag/navernews_200x200_20160804.png" property="og:image"/>
    <meta content="네이버 뉴스 검색 서비스" property="og:description"/>
    <meta content="summary" name="twitter:card"/>
    <meta content="뉴스검색 : 네이버 뉴스" name="twitter:title"/>
    <meta content="네이버 뉴스" name="twitter:site"/>
    <meta content="네이버 뉴스" name="twitter:creator"/>
    <meta content="http://static.news.naver.net/image/news/ogtag/navernews_200x200_20160804.png" name="twitter:image"/>
    <meta content="네이버 뉴스 검색 서비스" name="twitter:description"/>
    <title>아는형님 검색결과 : 네이버 뉴스</title>
    <link href="http://static.news.naver.net/image/news/2014/favicon/favicon.ico" rel="shortcut icon" type="image/x-icon"/>
    <!-- Sub main menu -->
    <style type="text/css">
    .search01{color:#2929D6} a.search01:link{color:#2929D6;text-decoration:underline} a.search01:visited{color:#800080;text-decoration:underline} a.search01:active{color:#2929D6F;text-decoration:underline} a.search01:hover{color:#2929D6;text-decoration:underline}
    .search02{color:#909090} a.search02:link{color:#909090;text-decoration:underline} a.search02:visited{color:#B2B2B2;text-decoration:underline} a.search02:active{color:#909090;text-decoration:underline} a.search02:hover{color:#CF4900;text-decoration:none}
    .search03{color:#2929D6} a.search03:link{color:#2929D6;text-decoration:none} a.search03:visited{color:#6A6A6A} a.search03:active{color:#2929D6F;text-decoration:underline} a.search03:hover{color:#2929D6;text-decoration:underline}
    </style>
    <link charset="euc-kr" href="http://static.news.naver.net/pnews/resources/20170525_171416/css/common.css" rel="stylesheet" type="text/css"/>
    <link charset="euc-kr" href="http://static.news.naver.net/pnews/resources/20170525_171416/css/news.css" rel="stylesheet" type="text/css"/>
    <script charset="euc-kr" src="http://static.news.naver.net/pnews/resources/20170525_171416/js/news.jindo.js" type="text/javascript"></script>
    <script type="text/javascript">
    document.domain='naver.com';
    var _MAIN_NEWS_MENU_ID='etc';
    var _MAIN_NEWS_SECTION_ID='104';
    var ccsrv='cc.naver.com';
    var nsc='news.etcs';
    var gnb_service='news';
    var gnb_logout='http://news.naver.com';
    var gnb_template='gnb_euckr';
    var gnb_brightness = 1;
    var gnb_one_naver = 1;
    var gnb_searchbox='on';
    var gnb_shortnick='on';
    
    var gnb_timestamp = "2017052816";
    </script>
    <script charset="euc-kr" src="http://static.news.naver.net/pnews/resources/20170525_171416/js/news.service.js" type="text/javascript"></script>
    <script type="text/javascript">
    var g_D = 0 ;
    
    function tt_sub_disable (o) { 
    	if (typeof(o.tt_sub) == "undefined") {
    		return false ; 
    	}
    	if ((typeof(o.tt_sub) == "object") && (o.tt_sub.length)) { 
    		var i ; 
    		for (var i=0; i<o.tt_sub.length; i++) {
    			o.tt_sub[i].disabled = true ; 
    		}
    	} else {
    		o.tt_sub.disabled = true ; 
    	}
    	return true ; 
    }
    
    var g_crt = "" ;
    
    function urlencode (q) {
    	return escape(q).replace(/\+/g, "%2B");
    }
    
    function urlexpand (url) { 
    	var href = document.location.href ; 
    	if (url == "") return href ; 
    	if (url.match(/^[-.A-Za-z]+:/)) return url ; 
    	if (url.charAt(0) == '#') return href.split("#")[0] + url ; 
    	if (url.charAt(0) == '?') return href.split("?")[0] + url ; 
    	if (url.charAt(0) == '/') return href.replace(/([^:\/])\/.*$/, "$1") + url ; 
    	return href.substring(0, href.lastIndexOf("/")+1) + url ; 
    }
    
    var g_query  = "%BE%C6%B4%C2%C7%FC%B4%D4";
    var g_tab    = "";
    var g_stab   = "";
    var g_puid   = "";
    var g_suid   = "";
    var g_sscode = "svc.news.all";
    
    function goCR (o, l, p, t) { 
    	var evt, sx = sy = px = py = -1 ; 
    	try { evt = window.event ; } catch (e) {} 
    	try { sx=evt.clientX-document.body.clientLeft, sy=evt.clientY-document.body.clientTop ; } catch (e) {}
    	try { px=document.body.scrollLeft+(sx<0?0:sx), py=document.body.scrollTop+(sy<0?0:sy) ; } catch (e) {}		
    	try { if (evt.screenX) px=evt.screenX ; if (evt.screenY) py=evt.screenY ; } catch (e) {}
    	tt_sub_disable(o) ; 
    	var m = g_D ? 2 : o.target && o.target!="_self" ? 0 : 1 ; 
    	if (p.indexOf("u=javascript") >= 0) {
    		t = true , m = 0 ; 
    	}
    	var l = o.href.indexOf("http://cr.naver.com/")==0 ? o.href : "http://cr.naver.com/"+(g_D ? "nr" : l)+"?q="+g_query+"&f="+g_tab+"&w="+g_stab+"&ssc="+g_sscode+"&p="+g_puid+"&s="+g_suid+"&m="+m+"&"+p+"&px="+px+"&py="+py+"&sx="+sx+"&sy="+sy+g_crt ;		
    	if (m && !t) {
    		var a = o.innerHTML ; o.href = l ; 
    		if (o.innerHTML != a) {
    			o.innerHTML = a ; 
    		}
    	} else if (document.images) {
    		(new Image()).src = l ;
    	}
    	return true ; 
    }
    
    function goOtherCR(o, p) {
    	return goCR(o, "rd", p, false);
    }
    
    function get_param(name) {
    	var args = document.location.search;
    	var re = new RegExp(name + "=([^\&]*)");
    
    	var arg = re.exec(args);
    	if (arg)
    		return arg[1];
    	else
    		return "";
    }
    
    function checkChildren(parent, start, end) {
    	var i;
    
    	for (i = start; i <= end; i++) {
            try {
    		    document.newssearch.elements[i].checked = parent.checked;
            } catch (e) {}
        }
    
    	if (!parent.checked) {
            try {
    		    document.newssearch.elements[0].checked = false;
            } catch (e) {}
        }
    }
    function checkParent(child, parent) {
    	if (!child.checked)
    	{
    		document.newssearch.elements[parent].checked = false;
    	}
    }
    
    function selectAction(f) {	
    	var delimeter = ":";
    	var detail = document.newssearch;
    	
    	/* 언론사 */
    	f.rcnews.value = "exist:";
    				
    	for (var index = 0; index < detail.newscode.length; index++) {
    		if (detail.newscode[index].checked && detail.newscode[index].value) {
    			f.rcnews.value = f.rcnews.value + detail.newscode[index].value + delimeter;
    		}
    	}
    	
    	/* 섹션 */
    	f.rcsection.value = "exist:";
    	
    	for (var index = 0; index < detail.categorycode.length; index++) {
    		if (detail.categorycode[index].checked) {
    			f.rcsection.value = f.rcsection.value + detail.categorycode[index].value + delimeter;
    		}
    	}
    }
    
    var timeCount = 0;
    
    function setRefreshTitle() {
    	var refreshPeriod = document.refresh_option.refresh_option1.value != 0 ?
    						document.refresh_option.refresh_option1.value :
    						document.refresh_option.refresh_option2.value;
    
    	var remainSecond = refreshPeriod - timeCount;
    	
    	if (refreshPeriod > 0 && remainSecond <= 0) {
    		document.newssearch.refresh.value = refreshPeriod;
    		//document.location.reload();
    		document.newssearch.submit();
    	}
    
        if (refreshPeriod > 0) {
    		remainMinute = Math.floor(remainSecond / 60);
    		remainMinute = remainMinute < 10 ? "0" + remainMinute : remainMinute;
    
    		remainSecond = Math.floor(remainSecond % 60);
    		remainSecond = remainSecond < 10 ? "0" + remainSecond : remainSecond;
    
    		jindo.$('remain_time').innerHTML = remainMinute + ":" + remainSecond;
    
    		jindo.$('remain').style.display = "";
    		jindo.$('passed').style.display = "none";
    	} else {
    		passedMinute = Math.floor(timeCount / 60);
    		passedMinute = passedMinute < 10 ? "0" + passedMinute : passedMinute;
    
    		passedSecond = Math.floor(timeCount % 60);
    		passedSecond = passedSecond < 10 ? "0" + passedSecond : passedSecond;
    
    		jindo.$('passed_time').innerHTML = passedMinute + ":" + passedSecond;
    
    		jindo.$('passed').style.display = "";
    		jindo.$('remain').style.display = "none";
    	}
    
    	timeCount++;
    }
    
    function setRefreshTime(refreshTime) {
    	if (!refreshTime) {
    		document.refresh_option.refresh_option1.value = 0;
    		document.refresh_option.refresh_option2.value = 0;
    	} else {
    		document.refresh_option.refresh_option1.value = refreshTime;
    		document.refresh_option.refresh_option2.value = refreshTime;
    	}
    }
    
    function check_search(f) {
    	try {
    		if ( f.qt[0].checked == true ) {
    			jindo.$('spanxc').style.display = "none";
    			f.xc.value = '';
    		} else {
    			jindo.$('spanxc').style.display = "";
    			f.xc.disabled = false;
    			if ( f.xc.value.length == 0 || event.keyCode > 200 ) {
    				f.qt[1].disabled = false;
    			} else {
    				f.qt[1].disabled = true;
    			}
    		}
    	} catch (e) {}
    }
    </script>
    <script type="text/javascript">
    
    (function(){
    	var id = "naver-splugin-sdk", t = new Date(), yyyy = t.getFullYear(), mm = t.getMonth() + 1, dd = t.getDate();
    	
    	var s = document.createElement("script"); 
    	s.id = id; 
    	s.type = "text/javascript", 
    	s.charset = "euc-kr", 
    	s.async = false;
     	s.src = ("http://spi.naver.net/js/release/ko_EUC-KR/splugin.js?" + yyyy + (mm < 10 ? '0' + mm : mm) + (dd < 10 ? '0' + dd : dd));
     
     	var d = document.getElementsByTagName('head')[0]; d.appendChild(s, d);
    })();
    
    jindo.$Fn(function() {
     	if (!window.__splugin) {
    		window.__splugin = SocialPlugIn_Core({
    		 	"evKey" : "naver_news_search",
    		 	"serviceName" : "뉴스"
    		});
    	}
    }).attach(window, "load");
    
    </script>
    </head>
    <body>
    <div id="wrap">
    <!-- 광고적용 태그 추가 -->
    <div id="da_base"></div>
    <div id="da_stake"></div>
    <div id="header">
    <!-- Skip navigation -->
    <div id="u_skip">
    <a href="#lnb" tabindex="1"><span>메인 메뉴로 바로가기</span></a>
    <a href="#main_content" tabindex="2"><span>본문으로 바로가기</span></a>
    </div>
    <!-- //Skip navigation -->
    <div class="snb_area">
    <div class="snb_inner">
    <div class="gnb_wrap">
    <!-- GNB 마크업 -->
    <div id="gnb"></div>
    <!-- //GNB 마크업 -->
    </div>
    <div id="snb_wrap">
    <h1><a class="h_logo nclicks(STA.naver)" href="http://www.naver.com/"><span class="blind">NAVER</span></a>
    <a class="h_news nclicks(STA.news)" href="http://news.naver.com/"><span class="blind">뉴스</span></a> </h1>
    <ul class="snb_related_service">
    <li><span class="snb_bdr"></span><a class="nclicks(STA.enter)" href="http://entertain.naver.com/home"><img alt="TV연예" height="19" src="http://static.news.naver.net/image/news/2016/06/snb_h_entertain.png" width="52"/></a></li>
    <li><span class="snb_bdr"></span><a class="nclicks(STA.sports)" href="http://sports.news.naver.com"><img alt="스포츠" height="19" src="http://static.news.naver.net/image/news/2016/06/snb_h_sports.png" width="48"/></a></li>
    <li><span class="snb_bdr"></span><a class="nclicks(STA.newsstand)" href="http://newsstand.naver.com"><img alt="뉴스스탠드" height="19" src="http://static.news.naver.net/image/news/2016/06/snb_h_newsstand.png" width="77"/></a></li>
    <li><span class="snb_bdr"></span><a class="nclicks(STA.weather)" href="http://weather.naver.com"><img alt="날씨" height="19" src="http://static.news.naver.net/image/news/2016/06/snb_h_weather.png" width="31"/></a></li>
    </ul>
    </div>
    </div>
    </div>
    <div class="lnb_area">
    <div class="lnb_inner">
    <div class="lnb_menu" id="lnb">
    <ul>
    <li><a class="m1 nclicks(LNB.home)" href="/main/home.nhn"><span class="tx"><span class="blind">뉴스홈</span></span> </a></li>
    <li><a class="m2 nclicks(LNB.flash)" href="/main/list.nhn?mode=LSD&amp;mid=sec&amp;sid1=001"><span class="tx"><span class="blind">속보</span></span> </a></li>
    <li><a class="m3 nclicks(LNB.pol)" href="/main/main.nhn?mode=LSD&amp;mid=shm&amp;sid1=100"><span class="tx"><span class="blind">정치</span></span> </a></li>
    <li><a class="m4 nclicks(LNB.eco)" href="/main/main.nhn?mode=LSD&amp;mid=shm&amp;sid1=101"><span class="tx"><span class="blind">경제</span></span> </a></li>
    <li><a class="m5 nclicks(LNB.soc)" href="/main/main.nhn?mode=LSD&amp;mid=shm&amp;sid1=102"><span class="tx"><span class="blind">사회</span></span> </a></li>
    <li><a class="m6 nclicks(LNB.lif)" href="/main/main.nhn?mode=LSD&amp;mid=shm&amp;sid1=103"><span class="tx"><span class="blind">생활/문화</span></span> </a></li>
    <li><a class="m7 nclicks(LNB.wor)" href="/main/main.nhn?mode=LSD&amp;mid=shm&amp;sid1=104"><span class="tx"><span class="blind">세계</span></span> </a></li>
    <li><a class="m8 nclicks(LNB.sci)" href="/main/main.nhn?mode=LSD&amp;mid=shm&amp;sid1=105"><span class="tx"><span class="blind">IT/과학</span></span> </a></li>
    <li><a class="m9 nclicks(LNB.opi)" href="/main/opinion/home.nhn"><span class="tx"><span class="blind">오피니언</span></span> </a></li>
    <li><a class="m10 nclicks(LNB.pho)" href="/main/photo/index.nhn?mid=pho"><span class="tx"><span class="blind">포토</span></span> </a></li>
    <li><a class="m11 nclicks(LNB.tv)" href="/main/tv/index.nhn?mid=tvh"><span class="tx"><span class="blind">TV</span></span> </a></li>
    <li><a class="m12 nclicks(LNB.ranking)" href="/main/ranking/popularDay.nhn?mid=etc&amp;sid1=111"><span class="tx"><span class="blind">랭킹뉴스</span></span> </a></li>
    </ul>
    <form action="/main/search/search.nhn" id="lnb.searchForm" method="get" name="lnb_searchForm">
    <fieldset>
    <legend>뉴스 검색</legend>
    <input accesskey="s" class="text_index" name="query" style="ime-mode:active;" title="뉴스 검색" type="text"/>
    <input name="ie" type="hidden" value="MS949"/>
    <button class="btn_search_lnb nclicks(LNB.search)" type="submit"><span class="tx"><span class="blind">검색</span></span></button>
    </fieldset>
    </form>
    </div>
    </div>
    </div>
    <div class="lnb">
    <span class="lnb_date"><strong>05</strong>.<strong>28</strong><span class="day">(일)</span></span>
    <div class="lnb_weather" id="lnb.weather" title="서울 27 °C
    인천 23 °C
    수원 26 °C
    대전 28 °C
    청주 28 °C
    강릉 30 °C
    춘천 28 °C
    광주 30 °C
    전주 29 °C
    부산 22 °C
    대구 30 °C
    제주 22 °C
    울산 26 °C
    울릉도 20 °C">
    <h3 class="blind">날씨정보</h3><ul>
    <li><a class="nclicks(nct.weather,,1)" href="http://weather.naver.com/rgn/cityWetrCity.nhn?cityRgnCd=CT001013"><img alt="맑음" height="26" src="http://static.naver.net/weather/images/w_icon/w_s1.gif" title="맑음" width="36"/>서울<span><em>27</em>°C</span></a></li>
    <li><a class="nclicks(nct.weather,,2)" href="http://weather.naver.com/rgn/cityWetrCity.nhn?cityRgnCd=CT001028"><img alt="맑음" height="26" src="http://static.naver.net/weather/images/w_icon/w_s1.gif" title="맑음" width="36"/>인천<span><em>23</em>°C</span></a></li>
    <li><a class="nclicks(nct.weather,,3)" href="http://weather.naver.com/rgn/cityWetrCity.nhn?cityRgnCd=CT001015"><img alt="맑음" height="26" src="http://static.naver.net/weather/images/w_icon/w_s1.gif" title="맑음" width="36"/>수원<span><em>26</em>°C</span></a></li>
    <li><a class="nclicks(nct.weather,,4)" href="http://weather.naver.com/rgn/cityWetrCity.nhn?cityRgnCd=CT006005"><img alt="맑음" height="26" src="http://static.naver.net/weather/images/w_icon/w_s1.gif" title="맑음" width="36"/>대전<span><em>28</em>°C</span></a></li>
    <li><a class="nclicks(nct.weather,,5)" href="http://weather.naver.com/rgn/cityWetrCity.nhn?cityRgnCd=CT005010"><img alt="맑음" height="26" src="http://static.naver.net/weather/images/w_icon/w_s1.gif" title="맑음" width="36"/>청주<span><em>28</em>°C</span></a></li>
    <li><a class="nclicks(nct.weather,,6)" href="http://weather.naver.com/rgn/cityWetrCity.nhn?cityRgnCd=CT004001"><img alt="구름많음" height="26" src="http://static.naver.net/weather/images/w_icon/w_s21.gif" title="구름많음" width="36"/>강릉<span><em>30</em>°C</span></a></li>
    <li><a class="nclicks(nct.weather,,7)" href="http://weather.naver.com/rgn/cityWetrCity.nhn?cityRgnCd=CT003007"><img alt="맑음" height="26" src="http://static.naver.net/weather/images/w_icon/w_s1.gif" title="맑음" width="36"/>춘천<span><em>28</em>°C</span></a></li>
    <li><a class="nclicks(nct.weather,,8)" href="http://weather.naver.com/rgn/cityWetrCity.nhn?cityRgnCd=CT011005"><img alt="맑음" height="26" src="http://static.naver.net/weather/images/w_icon/w_s1.gif" title="맑음" width="36"/>광주<span><em>30</em>°C</span></a></li>
    <li><a class="nclicks(nct.weather,,9)" href="http://weather.naver.com/rgn/cityWetrCity.nhn?cityRgnCd=CT010011"><img alt="맑음" height="26" src="http://static.naver.net/weather/images/w_icon/w_s1.gif" title="맑음" width="36"/>전주<span><em>29</em>°C</span></a></li>
    <li><a class="nclicks(nct.weather,,10)" href="http://weather.naver.com/rgn/cityWetrCity.nhn?cityRgnCd=CT008008"><img alt="맑음" height="26" src="http://static.naver.net/weather/images/w_icon/w_s1.gif" title="맑음" width="36"/>부산<span><em>22</em>°C</span></a></li>
    <li><a class="nclicks(nct.weather,,11)" href="http://weather.naver.com/rgn/cityWetrCity.nhn?cityRgnCd=CT007007"><img alt="맑음" height="26" src="http://static.naver.net/weather/images/w_icon/w_s1.gif" title="맑음" width="36"/>대구<span><em>30</em>°C</span></a></li>
    <li><a class="nclicks(nct.weather,,12)" href="http://weather.naver.com/rgn/cityWetrCity.nhn?cityRgnCd=CT012005"><img alt="맑음" height="26" src="http://static.naver.net/weather/images/w_icon/w_s1.gif" title="맑음" width="36"/>제주<span><em>22</em>°C</span></a></li>
    <li><a class="nclicks(nct.weather,,13)" href="http://weather.naver.com/rgn/cityWetrCity.nhn?cityRgnCd=CT008012"><img alt="맑음" height="26" src="http://static.naver.net/weather/images/w_icon/w_s1.gif" title="맑음" width="36"/>울산<span><em>26</em>°C</span></a></li>
    <li><a class="nclicks(nct.weather,,14)" href="http://weather.naver.com/rgn/cityWetrCity.nhn?cityRgnCd=CT009002"><img alt="구름많음" height="26" src="http://static.naver.net/weather/images/w_icon/w_s21.gif" title="구름많음" width="36"/>울릉도<span><em>20</em>°C</span></a></li>
    </ul>
    </div>
    <div class="lnb_today">
    <h3><a class="nclicks(nct.main)" href="http://news.naver.com/main/history/mainnews/index.nhn" title="주요뉴스">주요뉴스</a></h3>
    <div id="lnb.mainnews">
    <ul>
    <li>
    <a class="nclicks(nct.maintxt,,1)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=102&amp;oid=001&amp;aid=0009297173" title="'돈봉투 만찬' 감찰반, 이영렬·안태근 등 20여명 조사 완료">'돈봉투 만찬' 감찰반, 이영렬·안태근 등 20여명 조사 …</a>
    </li>
    <li>
    <a class="nclicks(nct.maintxt,,2)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=100&amp;oid=001&amp;aid=0009297057" title='국정委 "고위공직자 임용기준안·청문회 개선방안 마련할 것"'>국정委 "고위공직자 임용기준안·청문회 개선방안 마련할…</a>
    </li>
    <li>
    <a class="nclicks(nct.maintxt,,3)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=102&amp;oid=001&amp;aid=0009297099" title="위안부합의 지키기 나선 日…한일 장외 신경전 치열할 듯">위안부합의 지키기 나선 日…한일 장외 신경전 치열할 듯</a>
    </li>
    <li>
    <a class="nclicks(nct.maintxt,,4)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=100&amp;oid=052&amp;aid=0001015174" title='與 "협치 요청"·野, 파상공세…청문회 정국 분수령'>與 "협치 요청"·野, 파상공세…청문회 정국 분수령</a>
    </li>
    <li>
    <a class="nclicks(nct.maintxt,,5)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=103&amp;oid=052&amp;aid=0001015170" title=" 내일 기온 더 올라…자외선 주의"> 내일 기온 더 올라…자외선 주의</a>
    </li>
    <li>
    <a class="nclicks(nct.maintxt,,6)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=100&amp;oid=001&amp;aid=0009297168" title="'野자극할라'…文대통령, 총리 인준 앞두고 조각 속도조절">'野자극할라'…文대통령, 총리 인준 앞두고 조각 속도조…</a>
    </li>
    <li>
    <a class="nclicks(nct.maintxt,,7)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=100&amp;oid=079&amp;aid=0002971631" title='靑 "朴 탄핵 때 사용 특수활동비 30억 혼자 안 썼다"'>靑 "朴 탄핵 때 사용 특수활동비 30억 혼자 안 썼다"</a>
    </li>
    <li>
    <a class="nclicks(nct.maintxt,,8)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=102&amp;oid=056&amp;aid=0010461188" title="안산역 에스컬레이터 갑자기 중단…9명 병원 이송">안산역 에스컬레이터 갑자기 중단…9명 병원 이송</a>
    </li>
    <li>
    <a class="nclicks(nct.maintxt,,9)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=100&amp;oid=014&amp;aid=0003821086" title="朴 전 대통령, 혐의 전면 부인 전략 '藥 될까 毒 될까'">朴 전 대통령, 혐의 전면 부인 전략 '藥 될까 毒 될까'</a>
    </li>
    <li>
    <a class="nclicks(nct.maintxt,,10)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=104&amp;oid=003&amp;aid=0007980532" title="험악했던 G7 회의장…새벽 2시까지 회의에도 美 설득 실패">험악했던 G7 회의장…새벽 2시까지 회의에도 美 설득 실…</a>
    </li>
    <li>
    <a class="nclicks(nct.maintxt,,11)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=102&amp;oid=028&amp;aid=0002366262" title=" 초미세먼지 WHO 기준 적용하면 서울시 나쁨일수 10배 늘어 "> 초미세먼지 WHO 기준 적용하면 서울시 나쁨일수 10배 …</a>
    </li>
    <li>
    <a class="nclicks(nct.maintxt,,12)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=104&amp;oid=001&amp;aid=0009297123" title='제2의 맨체스터 테러 우려 증폭…"영국 내 지하디스트 2만.."'>제2의 맨체스터 테러 우려 증폭…"영국 내 지하디스트 2…</a>
    </li>
    <li>
    <a class="nclicks(nct.maintxt,,13)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=101&amp;oid=001&amp;aid=0009297103" title="보험사에 경품행사 고객정보 팔고, 당첨자 바꿔치기 하고">보험사에 경품행사 고객정보 팔고, 당첨자 바꿔치기 하고</a>
    </li>
    <li>
    <a class="nclicks(nct.maintxt,,14)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=100&amp;oid=018&amp;aid=0003836027" title="공공기관에 '사회적 가치' 평가 도입..성과연봉제 폐지">공공기관에 '사회적 가치' 평가 도입..성과연봉제 폐지</a>
    </li>
    <li>
    <a class="nclicks(nct.maintxt,,15)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=101&amp;oid=052&amp;aid=0001015184" title="'일단 사고 보자' 반품 급증 …30·40대 여성이 절반">'일단 사고 보자' 반품 급증 …30·40대 여성이 절반</a>
    </li>
    <li>
    <a class="nclicks(nct.maintxt,,16)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=102&amp;oid=001&amp;aid=0009296647" title="주민번호 뒷자리 30일부터 변경 가능…성별은 제외">주민번호 뒷자리 30일부터 변경 가능…성별은 제외</a>
    </li>
    <li>
    <a class="nclicks(nct.maintxt,,17)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=102&amp;oid=001&amp;aid=0009297154" title='울산 앞바다서 규모 2.7 지진…"피해 없을 듯"'>울산 앞바다서 규모 2.7 지진…"피해 없을 듯"</a>
    </li>
    <li>
    <a class="nclicks(nct.maintxt,,18)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=104&amp;oid=421&amp;aid=0002756163" title="유럽서 퍼지는 '안티 백신'…獨정부 &quot;안 맞추면 벌금&quot;">유럽서 퍼지는 '안티 백신'…獨정부 "안 맞추면 벌금"</a>
    </li>
    <li>
    <a class="nclicks(nct.maintxt,,19)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=101&amp;oid=421&amp;aid=0002755864" title="1조 돌파한 P2P 대출…투자 한도 '연 1천만원'으로 제한">1조 돌파한 P2P 대출…투자 한도 '연 1천만원'으로 제한</a>
    </li>
    <li>
    <a class="nclicks(nct.maintxt,,20)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=104&amp;oid=001&amp;aid=0009296469" title="필리핀 계엄령 소도시 교전 지속…두테르테, 반군에 대화제의">필리핀 계엄령 소도시 교전 지속…두테르테, 반군에 대화…</a>
    </li>
    <li>
    <a class="nclicks(nct.maintxt,,21)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=102&amp;oid=025&amp;aid=0002720425" title="구의역 사고 1년, 지체된 정의 논란">구의역 사고 1년, 지체된 정의 논란</a>
    </li>
    <li>
    <a class="nclicks(nct.maintxt,,22)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=102&amp;oid=025&amp;aid=0002720447" title='사흘 간 6600건 의견 모인 국민인수위…"무슨 의견이든.."'>사흘 간 6600건 의견 모인 국민인수위…"무슨 의견이든..…</a>
    </li>
    <li>
    <a class="nclicks(nct.maintxt,,23)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=101&amp;oid=028&amp;aid=0002366260" title=" 일자리 상황판 속 비정규직 비중 '32.8%'가 전부인가요? "> 일자리 상황판 속 비정규직 비중 '32.8%'가 전부인가요?…</a>
    </li>
    </ul>
    </div>
    </div>
    <ul class="lnb_side" tabindex="0">
    <li><a class="nclicks(nct.right1)" href="http://news.naver.com/main/hotissue/sectionList.nhn?mid=hot&amp;sid1=110&amp;cid=1019690" title="상식in뉴스">상식in뉴스</a></li>
    <li><a class="nclicks(nct.right2)" href="http://news.naver.com/main/officeList.nhn" title="언론사 뉴스">언론사 뉴스</a></li>
    <li><a class="nclicks(nct.right3)" href="http://newslibrary.naver.com/search/searchByDate.nhn" target="_blank" title="라이브러리">라이브러리</a></li>
    <li class="end"><a class="nclicks(nct.right4)" href="http://news.naver.com/main/history/mainnews/index.nhn" title="기사배열 이력">기사배열 이력</a></li>
    </ul>
    </div>
    </div>
    <hr/>
    <table cellpadding="0" cellspacing="0" class="container">
    <caption class="blind">뉴스검색</caption>
    <colgroup>
    <col></col>
    <col width="280"></col>
    </colgroup>
    <tr>
    <td class="content">
    <div class="srch_content">
    <script type="text/javascript">
    function toggleLayer(layerId) {
    	var layer = ( typeof layerId == 'string' ) ? jindo.$(layerId) : layerId;		
    	var	displayAttr = layer.getStyle("display") != "none" ? "none" : "block";		
    	layer.setStyle( {display : displayAttr} );
    }
    
    function period_check(val) {			
    	var today = new Date();
    	var oneMonthAgo = new Date();
    	oneMonthAgo.setDate(today.getDate() -30); // 한달을 무조건 30일로 생각한다.
    	var oneWeekAgo = new Date();
    	oneWeekAgo.setDate(today.getDate() -7);
    
    	if (val == "1") { //전체
    		document.newssearch.startDate.value = "";
    		document.newssearch.endDate.value = "";			
    	} else if (val == "2") { //최근 한달간
    		document.newssearch.startDate.value = getDateForm(oneMonthAgo);
    		document.newssearch.endDate.value = getDateForm(today);
    	} else if (val == "3") { //최근 일주일간
    		document.newssearch.startDate.value = getDateForm(oneWeekAgo);
    		document.newssearch.endDate.value = getDateForm(today);
    	} else if (val == "4") { //전체직접입력
    		document.newssearch.startDate.value = "1960-01-01";
    		document.newssearch.endDate.value = getDateForm(today);
    	} else if (val == "5") {
    	    return getDateForm(today);
        }
    }
    
    function getDateForm(DateValue) {
    	var rawYear = DateValue.getFullYear();
    	var rawMonth = DateValue.getMonth() + 1;
    	var rawDate = DateValue.getDate();
    	
    	if (rawMonth < 10) {	
    		rawMonth = "0" + rawMonth;
    	}
    
    	if (rawDate < 10) {	
    		rawDate = "0" + rawDate;
    	}
    	
    	return rawYear + "-" + rawMonth + "-" + rawDate;
    }
    	
    function validatePeriod() {
    	var checkStartDate = document.newssearch.startDate.value.replace("-", "");
    	checkStartDate = checkStartDate.replace("-", "");
    	
    	var checkEndDate = document.newssearch.endDate.value.replace("-", "");
    	checkEndDate = checkEndDate.replace("-", "");
    
    	if (checkStartDate.length == 8 && checkEndDate.length == 8) {
    		checkPeriod(checkStartDate, checkEndDate);
    	} else if (checkStartDate.length == "" && checkEndDate.length == "") {
    		document.newssearch.submit();
    	} else {		
    		if (checkStartDate == "") {
    			if (checkEndDate.length == 8) {
    				document.newssearch.startDate.value = "1960-01-01";					
    				checkPeriod("19600101", checkEndDate);
    			} else {
    				alert("연월일 표기 형식에 맞게 입력해 주세요(숫자 8자리).\n2010년 1월 15일의 경우, 20100115와 같이 입력해 주세요.");
    				document.newssearch.startDate.value = "1960-01-01";
    				document.newssearch.endDate.focus();
    			}
    		} else if (checkEndDate == "") {
    			if (checkStartDate.length == 8) {					
    				document.newssearch.endDate.value = period_check(5);
    				
    				var currentDate = document.newssearch.endDate.value.replace("-", "");
    				currentDate = currentDate.replace("-", ""); 					
    				checkPeriod(checkStartDate, currentDate); 
    			} else {
    				alert("연월일 표기 형식에 맞게 입력해 주세요(숫자 8자리).\n2010년 1월 15일의 경우, 20100115와 같이 입력해 주세요.");
    				document.newssearch.endDate.value = period_check(5);
    				document.newssearch.startDate.focus();
    			}
    		} else {
    			alert("연월일 표기 형식에 맞게 입력해 주세요(숫자 8자리).\n2010년 1월 15일의 경우, 20100115와 같이 입력해 주세요.");
    			
    			if (checkStartDate.length != 8) {					
    				document.newssearch.startDate.focus();
    			} else {
    				document.newssearch.endDate.focus();
    			}
    		}
    	}
    }
    
    function checkStartDateScope(startYear, startMonth, startDay, currentDate) { 
    	if (startYear > currentDate.substring(0,4)) {
    		document.newssearch.startDate.value = "1960-01-01";
    	} else if (startYear == currentDate.substring(0,4)) {
    		if (startMonth > currentDate.substring(5,7)) {
    			document.newssearch.startDate.value = "1960-01-01";
    		} else if (startMonth == currentDate.substring(5,7)) {
    			if (startDay > currentDate.substring(8,10)) {
    				document.newssearch.startDate.value = "1960-01-01";
    			}
    		}
    	}
    }
    
    function checkPeriodScope(startYear, startMonth, startDay, endYear, endMonth, endDay) {
    	var currentDate = period_check(5);
    	
    	if (startYear < 1960) {
    		document.newssearch.startDate.value = "1960-01-01";
    
    		if (endYear < 1960) {
    			document.newssearch.endDate.value = currentDate;
    		}
    	}
    
    	if (endYear > currentDate.substring(0,4)) {			
    		document.newssearch.endDate.value = currentDate;
    		checkStartDateScope(startYear, startMonth, startDay, currentDate);		
    	} else if (endYear == currentDate.substring(0,4)) {
    		 if (endMonth > currentDate.substring(5,7)) {			
    			 document.newssearch.endDate.value = currentDate;
    			 checkStartDateScope(startYear, startMonth, startDay, currentDate);
    		 } else if (endMonth == currentDate.substring(5,7)) {
    			 if (endDay > currentDate.substring(8,10)) {					 					 
    				 document.newssearch.endDate.value = currentDate;
    				 checkStartDateScope(startYear, startMonth, startDay, currentDate);		 							 							 					
    			 }
    		 }
    	}
    }
    
    function checkPeriod(originalStartDate, originalEndDate) {
    	var startYear = originalStartDate.substring(0,4);
    	var startMonth = originalStartDate.substring(4,6);
    	var startDay = originalStartDate.substring(6,8);
    	var endYear = originalEndDate.substring(0,4);
    	var endMonth = originalEndDate.substring(4,6);
    	var endDay = originalEndDate.substring(6,8);
    	
    	var isValidStartDate = checkDate(startYear, startMonth, startDay);		
    	var isValidEndDate = checkDate(endYear, endMonth, endDay);
    
    	if (isValidStartDate.type == "success" && isValidEndDate.type == "success") {			
    		if (startYear < endYear) {
    			checkPeriodScope(startYear, startMonth, startDay, endYear, endMonth, endDay);				
    			document.newssearch.submit();
    		} else if (startYear == endYear) {
    			if (startMonth < endMonth) {
    				checkPeriodScope(startYear, startMonth, startDay, endYear, endMonth, endDay);
    				document.newssearch.submit();
    			} else if (startMonth == endMonth) {
    				if ((startDay < endDay) || (startDay == endDay)) {
    					checkPeriodScope(startYear, startMonth, startDay, endYear, endMonth, endDay);
    					document.newssearch.submit();
    				} else if (startDay > endDay) {
    					alert("시작 날짜를 끝 날짜보다 빠르게 입력해 주세요.");
    					document.newssearch.startDate.focus();
    				}
    			} else if (startMonth > endMonth) {
    				alert("시작 날짜를 끝 날짜보다 빠르게 입력해 주세요.");
    				document.newssearch.startDate.focus();
    			}			
    		} else if (startYear > endYear) {
    			alert("시작 날짜를 끝 날짜보다 빠르게 입력해 주세요.");
    			document.newssearch.startDate.focus();
    		}
    	} else {		
    		if (isValidStartDate.type == "month" && isValidEndDate.type == "month") {
    			alert(isValidStartDate.msg);
    			document.newssearch.startDate.focus();				
    		} else if (isValidStartDate.type == "month") {
    			alert(isValidStartDate.msg);
    			document.newssearch.startDate.focus();				
    		} else if (isValidEndDate.type == "month") {
    			alert(isValidEndDate.msg);
    			document.newssearch.endDate.focus();				
    		} else {
    			if (isValidStartDate.type == "day" && isValidEndDate.type == "day") {
    				alert(isValidStartDate.msg);
    				document.newssearch.startDate.focus();
    			} else if (isValidStartDate.type == "day") {
    				alert(isValidStartDate.msg);
    				document.newssearch.startDate.focus();
    			} else if (isValidEndDate.type == "day") {
    				alert(isValidEndDate.msg);
    				document.newssearch.endDate.focus();
    			}
    		}
    	}
    }
    
    function checkDate(year, month, day) {
    	var ret = {"type":"success", "msg":""};
    	
    	if (month <= 0 || month > 12) {
    		ret.type = "month";
    		ret.msg = "날짜 입력을 다시 확인해 주세요.\n월의 경우 1~12까지만 입력해 주세요.";
    		
    		return ret;			
    	}
    	
    	if (month == 1 || month == 3 || month == 5 || month == 7 || month == 8 || month == 10 || month == 12) {
    		if (day <= 0 || day > 31){
    			ret.type = "day";
    			ret.msg = "날짜 입력을 다시 확인해 주세요.\n" + year + "년 " + parseInt(month, 10) + "월의 경우\n" + "1~31까지만 입력해 주세요.";
    			
    			return ret;
    		}
    	} else {
    	    if (month == 2) {			    
                   if ((year % 4 == 0 && year % 100 != 0) || year % 400 == 0) {
                       if (day <= 0 || day > 29){
                       	ret.type = "day";
                       	ret.msg = "날짜 입력을 다시 확인해 주세요.\n" + year + "년 " + parseInt(month, 10) + "월의 경우\n" + "1~29까지만 입력해 주세요.";
                       	
                       	return ret;
    				}
                   } else {
                       if (day <= 0 || day > 28){
                       	ret.type = "day";
                       	ret.msg = "날짜 입력을 다시 확인해 주세요.\n" + year + "년 " + parseInt(month, 10) + "월의 경우\n" + "1~28까지만 입력해 주세요.";
                       	
                       	return ret;
    				} 
                   }
               } else {
                   if (day <= 0 || day > 30) {
                   	ret.type = "day";
                   	ret.msg = "날짜 입력을 다시 확인해 주세요.\n" + year + "년 " + parseInt(month, 10) + "월의 경우\n" + "1~30까지만 입력해 주세요.";
                   	
                   	return ret;
    			}
    		}
    	}
    	
    	return ret;
    }
    
    function checkNumber() {		 		
    	if((event.keyCode < 48 ) || (event.keyCode > 57)) {
    		event.returnValue = false;			
    	}	
    }	
    
    function dateWithBar() {
    	if (document.newssearch.startDate.value.length == 8) {
    		document.newssearch.startDate.value = document.newssearch.startDate.value.substring(0,4) + "-" + document.newssearch.startDate.value.substring(4,6) + "-" + document.newssearch.startDate.value.substring(6,8);									
    	}
    
    	if (document.newssearch.endDate.value.length == 8) {
    		document.newssearch.endDate.value = document.newssearch.endDate.value.substring(0,4) + "-" + document.newssearch.endDate.value.substring(4,6) + "-" + document.newssearch.endDate.value.substring(6,8);
    	}
    }
    
    function startDateWithoutBar() {
    	changeRadioCheck();
    				
    	document.newssearch.startDate.value = document.newssearch.startDate.value.replace("-", "");
    	document.newssearch.startDate.value = document.newssearch.startDate.value.replace("-", "");
    	
    	moveCursorToEnd();
    }
    
    function endDateWithoutBar() {
    	changeRadioCheck();
    	
    	document.newssearch.endDate.value = document.newssearch.endDate.value.replace("-", "");
    	document.newssearch.endDate.value = document.newssearch.endDate.value.replace("-", "");
    
    	moveCursorToEnd();
    }
    
    function changeRadioCheck() {
    	if (document.newssearch.pd[0].checked == true) {
    		document.newssearch.pd[0].checked = false;
    	}
    
    	if (document.newssearch.pd[1].checked == true) {
    		document.newssearch.pd[1].checked = false;
    	}
    
    	if (document.newssearch.pd[2].checked == true) {
    		document.newssearch.pd[2].checked = false;
    	}
    
    	document.newssearch.pd[3].checked = true;
    }
    
    function moveCursorToEnd() {
    	var range;
    	
    	try {
    		if (window.getSelection) {
    			range = window.getSelection();				
    		} else if (document.selection && document.selection.createRange()) {
    			range = document.selection.createRange();
    			range.moveEnd('character', 999);		
    			range.setEndPoint('starttoend', range);
    			range.select();			
    		}		
    	} catch (e) {
    		// skip
    	}
    }
    
    function goNexSearch() {
    	var q = document.newssearch.query.value;
    	if (q == null || q == '') {
    		alert('검색어가 입력되지 않았습니다');
    		document.newssearch.query.focus();
    		return false;
    	}
    
    	var url = "http://search.naver.com/search.naver?where=nexearch&sm=nws_nex&query=" + encodeURIComponent(q);
    	window.open(url, '_blank');
    }
    </script>
    <fieldset class="search_box">
    <form action="/main/search/search.nhn" method="get" name="newssearch" onsubmit="validatePeriod(); return false;">
    <legend>뉴스 검색</legend>
    <input name="rcnews" type="hidden" value="exist:032:005:086:020:021:081:022:023:025:028:038:469:"/>
    <input name="refresh" type="hidden" value=""/>
    <input name="so" type="hidden" value="rel.dsc"/>
    <input name="stPhoto" type="hidden" value=""/>
    <input name="stPaper" type="hidden" value=""/>
    <input name="stRelease" type="hidden" value=""/>
    <input name="ie" type="hidden" value="MS949"/>
    <input name="detail" type="hidden" value="0"/>
    <div class="input_area">
    <div class="select_channel">
    <select id="selectbox_01_related" name="rcsection">
    <option value="">모든뉴스</option>
    <option value="exist:100">정치</option>
    <option value="exist:101">경제</option>
    <option value="exist:102">사회</option>
    <option value="exist:103">생활/문화</option>
    <option value="exist:104">세계</option>
    <option value="exist:105">IT/과학</option>
    <option value="exist:106">연예</option>
    <option value="exist:110">칼럼</option>
    <option value="exist:107">스포츠</option>
    </select>
    </div>
    <div class="fl">
    <input class="search_input" name="query" type="text" value="아는형님"/><input alt="검색" class="btn_search" src="http://static.news.naver.net/image/news/2011/btn_search.gif" type="image"/><a class="btn_search_all" onclick="goNexSearch(); return false;"><img alt="통합검색" border="0" height="21" src="http://static.news.naver.net/image/news/2011/btn_searchbox_all.gif" width="55"/></a>
    </div>
    </div>
    <p class="txt_help">
    <a href="/main/search/powersearch.nhn?query=아는형님&amp;rcnews=exist:032:005:086:020:021:081:022:023:025:028:038:469:&amp;rcsection=exist:&amp;startDate=2017-05-21&amp;endDate=2017-05-28&amp;pd=3&amp;sm=all.basic">뉴스 상세검색</a><span class="bar">l</span><a href="http://newslibrary.naver.com/search/searchDetails.nhn" target="_blank">뉴스 라이브러리 상세검색</a>
    <img alt="도움말" height="14" id="helpMessage" src="http://static.news.naver.net/image/news/2009/ico_info2_14x14.gif" width="14"/>
    <img alt="로그인이 필요한 서비스입니다" border="0" class="txt_login" height="35" id="messageLayer" src="http://static.news.naver.net/image/news/2009/txt_login_layer.gif" style="display:none;" width="162"/>
    </p>
    <dl class="search_option">
    <dt><strong>범위</strong></dt>
    <dd>
    <input checked="" id="range1" name="sm" type="radio" value="all.basic"/><label for="range1">제목과 본문</label><input id="range2" name="sm" type="radio" value="title.basic"/><label for="range2">제목에서만</label>
    </dd>
    <dt><strong>기간</strong></dt>
    <dd>
    <input checked="" id="date_a" name="pd" onclick="javascript:period_check('1');" type="radio" value="1"/><label for="date_a">전체</label><input id="date_m" name="pd" onclick="javascript:period_check('2');" type="radio" value="2"/><label for="date_m">최근 한달간</label><input checked="" id="date_w" name="pd" onclick="javascript:period_check('3');" type="radio" value="3"/><label for="date_w">최근 일주일간</label><input id="date_d" name="pd" onclick="javascript:period_check('4');" type="radio" value="4"/><label class="search_date_d" for="date_d">직접입력</label><input class="search_date" name="startDate" onblur="dateWithBar();" onfocus="startDateWithoutBar();" onkeypress="checkNumber();" style="IME-MODE:disabled" type="text" value="2017-05-21"/><span class="bar">~</span><input class="search_date" name="endDate" onblur="dateWithBar();" onfocus="endDateWithoutBar();" onkeypress="checkNumber();" style="IME-MODE:disabled" type="text" value="2017-05-28"/>
    </dd>
    </dl>
    </form></fieldset>
    <script type="text/javascript">
    function copy_clipboard(meintext) {
    	if (window.clipboardData) {
    		window.clipboardData.setData("Text", meintext);
    	} else if (window.netscape) {
    		netscape.security.PrivilegeManager.enablePrivilege('UniversalXPConnect');
    		var clip = Components.classes['@mozilla.org/widget/clipboard;1'].createInstance(Components.interfaces.nsIClipboard);
    		if (!clip) {
    			return;
    		}
    		var trans = Components.classes['@mozilla.org/widget/transferable;1'].createInstance(Components.interfaces.nsITransferable);
    		if (!trans) {
    			return;
    		}
    		trans.addDataFlavor('text/unicode');
    		var str = new Object();
    		var len = new Object();
    		var str = Components.classes["@mozilla.org/supports-string;1"].createInstance(Components.interfaces.nsISupportsString);
    
    		var copytext = meintext;
    		str.data = copytext;
    		trans.setTransferData("text/unicode", str, copytext.length * 2);
    		var clipid = Components.interfaces.nsIClipboard;
    		if (!clip) {
    			return false;
    		}
    		clip.setData(trans, null, clipid.kGlobalClipboard);
    	}
    	alert("RSS 주소가 복사되었습니다. RSS Reader 프로그램에 입력하시기 바랍니다.");
    	return false;
    }
    </script>
    <div class="search_tab">
    <a class="tab_tx on" href="search.nhn?query=%BE%C6%B4%C2%C7%FC%B4%D4&amp;st=news.all&amp;q_enc=EUC-KR&amp;r_enc=UTF-8&amp;r_format=xml&amp;rp=none&amp;sm=all.basic&amp;ic=all&amp;so=rel.dsc&amp;rcnews=exist:032:005:086:020:021:081:022:023:025:028:038:469:&amp;rcsection=exist:&amp;stDate=range:20170521:20170528&amp;detail=0&amp;pd=3&amp;r_cluster2_start=1&amp;r_cluster2_display=10&amp;start=1&amp;display=10&amp;startDate=2017-05-21&amp;endDate=2017-05-28&amp;dnaSo=rel.dsc"><span>최신뉴스 검색</span></a><a class="tab_tx" href="dna.nhn?query=%BE%C6%B4%C2%C7%FC%B4%D4&amp;st=dna&amp;q_enc=EUC-KR&amp;r_enc=UTF-8&amp;r_format=xml&amp;rp=general.svc&amp;sm=all.basic&amp;ic=basic&amp;so=rel.dsc&amp;rcnews=exist:032:005:086:020:021:081:022:023:025:028:038:469:&amp;rcsection=exist:&amp;stDate=range:20170521:20170528&amp;detail=0&amp;pd=3&amp;r_cluster2_start=1&amp;r_cluster2_display=10&amp;start=1&amp;display=10&amp;startDate=2017-05-21&amp;endDate=2017-05-28&amp;dnaSo=rel.dsc"><span>뉴스 라이브러리 검색</span></a><!-- <a href="paysearch.nhn?query=%BE%C6%B4%C2%C7%FC%B4%D4&st=newsp.all&q_enc=EUC-KR&r_enc=UTF-8&r_format=xml&rp=none&sm=all.basic&ic=all&so=rel.dsc&rcnews=exist:032:005:086:020:021:081:022:023:025:028:038:469:&rcsection=exist:&stDate=range:20170521:20170528&detail=0&pd=3&r_cluster2_start=1&r_cluster2_display=10&start=1&display=10&startDate=2017-05-21&endDate=2017-05-28&dnaSo=rel.dsc" class="tab_tx"><span>유료뉴스 검색</span></a>	 -->
    <div class="tab_option">
    <a href="/main/search/redirect.nhn?where=rss&amp;query=아는형님&amp;sm=all.basic" target="_blank">RSS</a><span>|</span><a href="#" onclick='return copy_clipboard("/main/search/redirect.nhn?where=rss&amp;query=아는형님&amp;sm=all.basic");'>주소복사</a><span>|</span><a href="#" onclick="news.external.OPS.helpNews(); return false;" title="새창">도움말</a>
    </div>
    </div>
    <div class="result_tab">
    <a class="on" href="?query=%BE%C6%B4%C2%C7%FC%B4%D4&amp;st=news.all&amp;q_enc=EUC-KR&amp;r_enc=UTF-8&amp;r_format=xml&amp;rp=none&amp;sm=all.basic&amp;ic=all&amp;so=rel.dsc&amp;rcnews=exist:032:005:086:020:021:081:022:023:025:028:038:469:&amp;rcsection=exist:&amp;stDate=range:20170521:20170528&amp;detail=0&amp;pd=3&amp;r_cluster2_start=1&amp;r_cluster2_display=10&amp;start=1&amp;display=10&amp;startDate=2017-05-21&amp;endDate=2017-05-28&amp;dnaSo=rel.dsc">뉴스</a><span>|</span><a class="" href="?query=%BE%C6%B4%C2%C7%FC%B4%D4&amp;st=news.all&amp;q_enc=EUC-KR&amp;r_enc=UTF-8&amp;r_format=xml&amp;rp=none&amp;sm=all.basic&amp;ic=all&amp;so=rel.dsc&amp;stPhoto=exist:1&amp;rcnews=exist:032:005:086:020:021:081:022:023:025:028:038:469:&amp;rcsection=exist:&amp;stDate=range:20170521:20170528&amp;detail=1&amp;pd=3&amp;r_cluster2_start=1&amp;r_cluster2_display=10&amp;start=1&amp;display=10&amp;startDate=2017-05-21&amp;endDate=2017-05-28&amp;dnaSo=rel.dsc">포토뉴스</a><span>|</span><a class="" href="?query=%BE%C6%B4%C2%C7%FC%B4%D4&amp;st=news.all&amp;q_enc=EUC-KR&amp;r_enc=UTF-8&amp;r_format=xml&amp;rp=none&amp;sm=all.basic&amp;ic=all&amp;so=rel.dsc&amp;stPhoto=exist:2&amp;rcnews=exist:032:005:086:020:021:081:022:023:025:028:038:469:&amp;rcsection=exist:&amp;stDate=range:20170521:20170528&amp;detail=1&amp;pd=3&amp;r_cluster2_start=1&amp;r_cluster2_display=10&amp;start=1&amp;display=10&amp;startDate=2017-05-21&amp;endDate=2017-05-28&amp;dnaSo=rel.dsc">TV뉴스</a><span>|</span><a class="" href="?query=%BE%C6%B4%C2%C7%FC%B4%D4&amp;st=news.all&amp;q_enc=EUC-KR&amp;r_enc=UTF-8&amp;r_format=xml&amp;rp=none&amp;sm=all.basic&amp;ic=all&amp;so=rel.dsc&amp;stPaper=exist:1&amp;rcnews=exist:032:005:086:020:021:081:022:023:025:028:038:469:&amp;rcsection=exist:&amp;stDate=range:20170521:20170528&amp;detail=0&amp;pd=3&amp;r_cluster2_start=1&amp;r_cluster2_display=10&amp;start=1&amp;display=10&amp;startDate=2017-05-21&amp;endDate=2017-05-28&amp;dnaSo=rel.dsc"><img alt="신문게재기사" height="12" src="http://static.news.naver.net/image/news/2010/ico_newspaper.gif" width="15"/>신문게재기사</a><span>|</span><a class="" href="?query=%BE%C6%B4%C2%C7%FC%B4%D4&amp;st=news.all&amp;q_enc=EUC-KR&amp;r_enc=UTF-8&amp;r_format=xml&amp;rp=none&amp;sm=all.basic&amp;ic=all&amp;so=rel.dsc&amp;stRelease=exist:1&amp;rcnews=exist:032:005:086:020:021:081:022:023:025:028:038:469:&amp;rcsection=exist:&amp;stDate=range:20170521:20170528&amp;detail=0&amp;pd=3&amp;r_cluster2_start=1&amp;r_cluster2_display=10&amp;start=1&amp;display=10&amp;startDate=2017-05-21&amp;endDate=2017-05-28&amp;dnaSo=rel.dsc">보도자료</a>
    </div>
    <div class="result_header">
    <h3>검색결과</h3>
    <span class="result_num">
    					
    						(1 ~ 10 / 33건)
    						
    					
    				</span>
    <span class="list_order_by">
    <a class="on" href="?query=%BE%C6%B4%C2%C7%FC%B4%D4&amp;st=news.all&amp;q_enc=EUC-KR&amp;r_enc=UTF-8&amp;r_format=xml&amp;rp=none&amp;sm=all.basic&amp;ic=all&amp;so=rel.dsc&amp;rcnews=exist:032:005:086:020:021:081:022:023:025:028:038:469:&amp;rcsection=exist:&amp;stDate=range:20170521:20170528&amp;detail=0&amp;pd=3&amp;r_cluster2_start=1&amp;r_cluster2_display=10&amp;start=1&amp;display=10&amp;startDate=2017-05-21&amp;endDate=2017-05-28&amp;dnaSo=rel.dsc">관련도순</a>
    <span class="bar">|</span>
    <a class="" href="?query=%BE%C6%B4%C2%C7%FC%B4%D4&amp;st=news.all&amp;q_enc=EUC-KR&amp;r_enc=UTF-8&amp;r_format=xml&amp;rp=none&amp;sm=all.basic&amp;ic=all&amp;so=datetime.dsc&amp;rcnews=exist:032:005:086:020:021:081:022:023:025:028:038:469:&amp;rcsection=exist:&amp;stDate=range:20170521:20170528&amp;detail=0&amp;pd=3&amp;r_cluster2_start=1&amp;r_cluster2_display=10&amp;start=1&amp;display=10&amp;startDate=2017-05-21&amp;endDate=2017-05-28&amp;dnaSo=rel.dsc">최신순</a>
    </span>
    <span class="btn_view_type on_view_type1">
    <a class="view1" href="?query=%BE%C6%B4%C2%C7%FC%B4%D4&amp;st=news.all&amp;q_enc=EUC-KR&amp;r_enc=UTF-8&amp;r_format=xml&amp;rp=none&amp;sm=all.basic&amp;ic=all&amp;so=rel.dsc&amp;rcnews=exist:032:005:086:020:021:081:022:023:025:028:038:469:&amp;rcsection=exist:&amp;stDate=range:20170521:20170528&amp;detail=0&amp;pd=3&amp;r_cluster2_start=1&amp;r_cluster2_display=10&amp;start=1&amp;display=10&amp;startDate=2017-05-21&amp;endDate=2017-05-28&amp;dnaSo=rel.dsc">제목과 내용보기</a>
    <a class="view2" href="?query=%BE%C6%B4%C2%C7%FC%B4%D4&amp;st=news.all&amp;q_enc=EUC-KR&amp;r_enc=UTF-8&amp;r_format=xml&amp;rp=none&amp;sm=all.basic&amp;ic=all&amp;so=rel.dsc&amp;rcnews=exist:032:005:086:020:021:081:022:023:025:028:038:469:&amp;rcsection=exist:&amp;stDate=range:20170521:20170528&amp;detail=1&amp;pd=3&amp;r_cluster2_start=1&amp;r_cluster2_display=10&amp;start=1&amp;display=10&amp;startDate=2017-05-21&amp;endDate=2017-05-28&amp;dnaSo=rel.dsc">제목만 보기</a>
    </span>
    <form name="refresh_option"><p>
    <!-- checked refresh -->
    <font color="#cf4900" id="ln1">최근 일주일</font> 동안의
    										
    						
    					
    				
    					
    			
    					
    						
    										
    					
    				
    					대상으로 뉴스를 검색 하였습니다.
    					<!-- totalcnt > 0 이면 검색 시각 관련 메시지 출력 -->
    <span id="remain" style="display:none; margin:0px"><font id="ln1"><br/>다음 자동 검색까지 남은 시간 <span id="remain_time"></span></font>   
    						    <select id="ln1" name="refresh_option2" onchange="setRefreshTime(this.value); timeCount=0;">
    <option value="0">자동고침</option>
    <option value="60">1분 마다</option>
    <option value="300">5분 마다</option>
    <option value="1800">30분 마다</option>
    </select>
    </span>
    <span id="passed">
    							마지막 검색 시각으로부터 <strong><span id="passed_time"></span></strong>가 지났습니다. <br/> 
    							편리한 실시간 기사 검색을 원하시면
    						    <select id="ln1" name="refresh_option1" onchange="setRefreshTime(this.value); timeCount=0;">
    <option value="0">자동고침</option>
    <option value="60">1분 마다</option>
    <option value="300">5분 마다</option>
    <option value="1800">30분 마다</option>
    </select>
    							설정을 해주세요.				
    						</span>
    <script type="text/javascript">
    						    setRefreshTitle();
    						    setInterval("setRefreshTitle()", 1000);
    						</script>
    </p></form>
    </div>
    <div class="srch_result_area" id="search_div">
    <ul class="srch_lst">
    <li>
    <a class="thmb" href="http://en.seoul.co.kr/news/newsView.php?id=20170528500034&amp;wlog_tag3=naver">
    <img onerror='javascript:this.src="http://static.news.naver.net/image/news/2009/press/empty.png"; document.getElementById("image_list_081_0002824625").removeAttribute("class");' src="http://imgnews.naver.net/image/thumb80/081/2017/05/28/2824625.jpg"/><span class="vm"></span>
    </a>
    <div class="ct">
    <a class="tit" href="http://en.seoul.co.kr/news/newsView.php?id=20170528500034&amp;wlog_tag3=naver" onclick="" target="_blank">‘<b>아는형님</b>’ 오현경, “강호동이 이상형..고백했으면 사귀었을 것”</a>
    <div class="info">
    <span class="pht"></span>
    <span class="press">서울신문</span> <span class="bar"></span> <span class="time">2시간전</span> <span class="bar"></span> <a class="go_naver" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=106&amp;oid=081&amp;aid=0002824625" onclick="" target="_blank">네이버뉴스</a>
    <!-- 개인화플러그인 -->
    <div class="head_social share_area">
    <span class="bar"></span>
    <a class="btn_share btn_social naver-splugin nclicks(STA.share)" data-mail-display="off" data-me-display="off" data-oninitialize="splugin_oninitialize('this');" data-option="{baseElement:'spiButton0', layerPosition:'outside-bottom', align:'right', top:4, left:0, marginLeft:8, marginTop:10}" data-service-name="뉴스" data-source-name="서울신문" data-style="unity-v2" data-title="‘아는형님’ 오현경, “강호동이 이상형..고백했으면 사귀었을 것”" data-url="http://en.seoul.co.kr/news/newsView.php?id=20170528500034&amp;wlog_tag3=naver" href="#" id="spiButton0" title="소셜공유하기">
    <span class="blind naver-splugin-c">소셜공유하기</span>
    </a>
    </div>
    <!-- 개인화플러그인 -->
    </div>
    <p class="dsc">
    				[서울신문 En] ‘<b>아는형님</b>’ 오현경배우 오현경이 “강호동이 이상형이다”고 고백했다. 오현경은 27일 JTBC ‘아는 형님’에 출연해 이같이 말했다. 강호동과 25년 지기 친구인 오현경은 “나보고 반한 적 없어?”라고 ‘기습’ 질문했다. 이에 강호동은 고개를 숙이고 손수건으로... 
    			</p>
    <!-- [D] 관련 뉴스 -->
    <div class="related_group">
    <strong class="blind">관련 뉴스</strong>
    <ul class="related_lst">
    <li>
    <span class="ico_bu"></span><a href="http://news.donga.com/3/all/20170528/84597660/2" target="_blank"><b>아는형님</b> 오현경 “강호동 내 이상형…고백했으면 사귀었을 것”</a>
    <p class="info">
    <span class="press">동아일보</span> <span class="bar"></span> <span class="time">3시간전</span> <span class="bar"></span> <a class="go_naver" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=106&amp;oid=020&amp;aid=0003068039" onclick="" target="_blank">네이버뉴스</a>
    </p>
    </li>
    </ul>
    </div>
    <ul class="srch_lst">
    <li>
    <a class="thmb" href="http://news.donga.com/3/all/20170528/84596732/2">
    <img onerror='javascript:this.src="http://static.news.naver.net/image/news/2009/press/empty.png"; document.getElementById("image_list_020_0003068035").removeAttribute("class");' src="http://imgnews.naver.net/image/thumb80/020/2017/05/28/3068035.jpg"/><span class="vm"></span>
    </a>
    <div class="ct">
    <a class="tit" href="http://news.donga.com/3/all/20170528/84596732/2" onclick="" target="_blank">‘아는 형님’ 오현경, 별명 ‘엉뚱이’…“힙 사이즈 36, 엉덩이 뚱뚱해서 창피”</a>
    <div class="info">
    <span class="pht"></span>
    <span class="press">동아일보</span> <span class="bar"></span> <span class="time">5시간전</span> <span class="bar"></span> <a class="go_naver" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=106&amp;oid=020&amp;aid=0003068035" onclick="" target="_blank">네이버뉴스</a>
    <!-- 개인화플러그인 -->
    <div class="head_social share_area">
    <span class="bar"></span>
    <a class="btn_share btn_social naver-splugin nclicks(STA.share)" data-mail-display="off" data-me-display="off" data-oninitialize="splugin_oninitialize('this');" data-option="{baseElement:'spiButton1', layerPosition:'outside-bottom', align:'right', top:4, left:0, marginLeft:8, marginTop:10}" data-service-name="뉴스" data-source-name="동아일보" data-style="unity-v2" data-title="‘아는 형님’ 오현경, 별명 ‘엉뚱이’…“힙 사이즈 36, 엉덩이 뚱뚱해서 창피”" data-url="http://news.donga.com/3/all/20170528/84596732/2" href="#" id="spiButton1" title="소셜공유하기">
    <span class="blind naver-splugin-c">소셜공유하기</span>
    </a>
    </div>
    <!-- 개인화플러그인 -->
    </div>
    <p class="dsc">
    				[동아닷컴] 사진=JTBC 예능 프로그램 ‘아는 형님’ 캡처화면배우 오현경은 자신의 별명이 ‘엉뚱이’라면서 남다른 몸매를 과시했다. 오현경은 27일 방송된 JTBC 예능 프로그램 ‘아는 형님’에 출연해 자신의 별명을 ‘엉뚱이’라고 소개했다. 이에 방송인 서장훈은 “엉덩이가... 
    			</p>
    <!-- [D] 관련 뉴스 -->
    <div class="related_group">
    <strong class="blind">관련 뉴스</strong>
    <ul class="related_lst">
    </ul>
    </div>
    <ul class="srch_lst">
    <li>
    <div class="ct">
    <a class="tit" href="http://news.chosun.com/site/data/html_dir/2017/05/27/2017052701457.html" onclick="" target="_blank">'<b>아는형님</b>' 딘딘, 마동석과 친분샷…"마블리, 마요미"</a>
    <div class="info">
    <span class="pht"></span>
    <span class="press">조선일보</span> <span class="bar"></span> <span class="time">16시간전</span> <span class="bar"></span> <a class="go_naver" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=004&amp;oid=023&amp;aid=0003283677" onclick="" target="_blank">네이버뉴스</a>
    <!-- 개인화플러그인 -->
    <div class="head_social share_area">
    <span class="bar"></span>
    <a class="btn_share btn_social naver-splugin nclicks(STA.share)" data-mail-display="off" data-me-display="off" data-oninitialize="splugin_oninitialize('this');" data-option="{baseElement:'spiButton2', layerPosition:'outside-bottom', align:'right', top:4, left:0, marginLeft:8, marginTop:10}" data-service-name="뉴스" data-source-name="조선일보" data-style="unity-v2" data-title="'아는형님' 딘딘, 마동석과 친분샷…&quot;마블리, 마요미&quot;" data-url="http://news.chosun.com/site/data/html_dir/2017/05/27/2017052701457.html" href="#" id="spiButton2" title="소셜공유하기">
    <span class="blind naver-splugin-c">소셜공유하기</span>
    </a>
    </div>
    <!-- 개인화플러그인 -->
    </div>
    <p class="dsc">
    				‘<b>아는형님</b>’에 출연한 딘딘이 마동석을 언급해 화제를 모은 가운데 두 사람이 찍은 사진이 화제다. 딘딘은 자신의 인스타그램에 마동석과 찍은 사진을 올린 적이 있다. 그는 “영화 ‘함정’ 시사회! 마블리 마요미 마동석 형님. 영화 대박나세요! 흥미딘딘하군”이라는 글과 함께 사진을... 
    			</p>
    <!-- [D] 관련 뉴스 -->
    <div class="related_group">
    <strong class="blind">관련 뉴스</strong>
    <ul class="related_lst">
    </ul>
    </div>
    <ul class="srch_lst">
    <li>
    <a class="thmb" href="http://www.segye.com/content/html/2017/05/27/20170527001011.html?OutUrl=naver">
    <img onerror='javascript:this.src="http://static.news.naver.net/image/news/2009/press/empty.png"; document.getElementById("image_list_022_0003177038").removeAttribute("class");' src="http://imgnews.naver.net/image/thumb80/022/2017/05/27/3177038.jpg"/><span class="vm"></span>
    </a>
    <div class="ct">
    <a class="tit" href="http://www.segye.com/content/html/2017/05/27/20170527001011.html?OutUrl=naver" onclick="" target="_blank">'<b>아는형님</b>' 딘딘, 마동석과 부자지간 뺨치는 친분샷 '훈훈'</a>
    <div class="info">
    <span class="pht"></span>
    <span class="press">세계일보</span> <span class="bar"></span> <span class="time">19시간전</span> <span class="bar"></span> <a class="go_naver" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=106&amp;oid=022&amp;aid=0003177038" onclick="" target="_blank">네이버뉴스</a>
    <!-- 개인화플러그인 -->
    <div class="head_social share_area">
    <span class="bar"></span>
    <a class="btn_share btn_social naver-splugin nclicks(STA.share)" data-mail-display="off" data-me-display="off" data-oninitialize="splugin_oninitialize('this');" data-option="{baseElement:'spiButton3', layerPosition:'outside-bottom', align:'right', top:4, left:0, marginLeft:8, marginTop:10}" data-service-name="뉴스" data-source-name="세계일보" data-style="unity-v2" data-title="'아는형님' 딘딘, 마동석과 부자지간 뺨치는 친분샷 '훈훈'" data-url="http://www.segye.com/content/html/2017/05/27/20170527001011.html?OutUrl=naver" href="#" id="spiButton3" title="소셜공유하기">
    <span class="blind naver-splugin-c">소셜공유하기</span>
    </a>
    </div>
    <!-- 개인화플러그인 -->
    </div>
    <p class="dsc">
    				사진=딘딘 인스타그램 '<b>아는형님</b>' 딘딘이 마동석을 언급 해 화제를 모은 가운데 두 사람의 친분이 돋보이는 다정샷이 화제다. 딘딘은 과거... 한편 27일 방송된 JTBC '아는 형님'에서 딘딘이 마동석을 언급했다. 마동석과 같은 소속사인 딘딘이 마동석에게 동반 출연을 요청했는데 성사되지... 
    			</p>
    <!-- [D] 관련 뉴스 -->
    <div class="related_group">
    <strong class="blind">관련 뉴스</strong>
    <ul class="related_lst">
    </ul>
    </div>
    <ul class="srch_lst">
    <li>
    <a class="thmb" href="http://en.seoul.co.kr/news/newsView.php?id=20170527500058&amp;wlog_tag3=naver">
    <img onerror='javascript:this.src="http://static.news.naver.net/image/news/2009/press/empty.png"; document.getElementById("image_list_081_0002824583").removeAttribute("class");' src="http://imgnews.naver.net/image/thumb80/081/2017/05/27/2824583.jpg"/><span class="vm"></span>
    </a>
    <div class="ct">
    <a class="tit" href="http://en.seoul.co.kr/news/newsView.php?id=20170527500058&amp;wlog_tag3=naver" onclick="" target="_blank">‘아는 형님’ 오현경, 48세 맞아? 놀라운 동안 외모 “나이 들어도 예쁜… 절세미인”</a>
    <div class="info">
    <span class="pht"></span>
    <span class="press">서울신문</span> <span class="bar"></span> <span class="time">19시간전</span> <span class="bar"></span> <a class="go_naver" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=106&amp;oid=081&amp;aid=0002824583" onclick="" target="_blank">네이버뉴스</a>
    <!-- 개인화플러그인 -->
    <div class="head_social share_area">
    <span class="bar"></span>
    <a class="btn_share btn_social naver-splugin nclicks(STA.share)" data-mail-display="off" data-me-display="off" data-oninitialize="splugin_oninitialize('this');" data-option="{baseElement:'spiButton4', layerPosition:'outside-bottom', align:'right', top:4, left:0, marginLeft:8, marginTop:10}" data-service-name="뉴스" data-source-name="서울신문" data-style="unity-v2" data-title="‘아는 형님’ 오현경, 48세 맞아? 놀라운 동안 외모 “나이 들어도 예쁜… 절세미인”" data-url="http://en.seoul.co.kr/news/newsView.php?id=20170527500058&amp;wlog_tag3=naver" href="#" id="spiButton4" title="소셜공유하기">
    <span class="blind naver-splugin-c">소셜공유하기</span>
    </a>
    </div>
    <!-- 개인화플러그인 -->
    </div>
    <p class="dsc">
    				27일 방송된 JTBC ‘아는 형님’에서는 배우 오현경과 가수 딘딘이 새로운 전학생으로 등장했다. 이날 오현경의 등장에 강호동은... 사진=JTBC ‘아는 형님’ 방송 캡처 연예팀 seoulen@seoul.co.kr ▶ [서울EN 바로가기] [페이스북] ▶ [스타 화보 보기] ⓒ 서울신문(www.seoul.co.kr), 무단전재 및... 
    			</p>
    <!-- [D] 관련 뉴스 -->
    <div class="related_group">
    <strong class="blind">관련 뉴스</strong>
    <ul class="related_lst">
    </ul>
    </div>
    <ul class="srch_lst">
    <li>
    <div class="ct">
    <a class="tit" href="http://news.chosun.com/site/data/html_dir/2017/05/27/2017052701462.html" onclick="" target="_blank">'<b>아는형님</b>' 오현경, 정시아와 동안 대결…"끝판왕 누구"</a>
    <div class="info">
    <span class="pht"></span>
    <span class="press">조선일보</span> <span class="bar"></span> <span class="time">16시간전</span> <span class="bar"></span> <a class="go_naver" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=004&amp;oid=023&amp;aid=0003283679" onclick="" target="_blank">네이버뉴스</a>
    <!-- 개인화플러그인 -->
    <div class="head_social share_area">
    <span class="bar"></span>
    <a class="btn_share btn_social naver-splugin nclicks(STA.share)" data-mail-display="off" data-me-display="off" data-oninitialize="splugin_oninitialize('this');" data-option="{baseElement:'spiButton5', layerPosition:'outside-bottom', align:'right', top:4, left:0, marginLeft:8, marginTop:10}" data-service-name="뉴스" data-source-name="조선일보" data-style="unity-v2" data-title="'아는형님' 오현경, 정시아와 동안 대결…&quot;끝판왕 누구&quot;" data-url="http://news.chosun.com/site/data/html_dir/2017/05/27/2017052701462.html" href="#" id="spiButton5" title="소셜공유하기">
    <span class="blind naver-splugin-c">소셜공유하기</span>
    </a>
    </div>
    <!-- 개인화플러그인 -->
    </div>
    <p class="dsc">
    				‘<b>아는형님</b>’에 나온 배우 오현경의 동안 외모가 눈길을 끈다. 오현경은 최근 자신의 인스타그램에 배우 정시아와 함께 찍은 사진을 올렸다. 오현경은 “베트남 다낭에서 시아와”라는 글을 적었다. 사진을 보면 오현경과 정시아는 환한 미소를 지으며 카메라를 바라보고 있다. 특히 다정한... 
    			</p>
    <!-- [D] 관련 뉴스 -->
    <div class="related_group">
    <strong class="blind">관련 뉴스</strong>
    <ul class="related_lst">
    </ul>
    </div>
    <ul class="srch_lst">
    <li>
    <a class="thmb" href="http://www.segye.com/content/html/2017/05/27/20170527001053.html?OutUrl=naver">
    <img onerror='javascript:this.src="http://static.news.naver.net/image/news/2009/press/empty.png"; document.getElementById("image_list_022_0003177041").removeAttribute("class");' src="http://imgnews.naver.net/image/thumb80/022/2017/05/27/3177041.jpg"/><span class="vm"></span>
    </a>
    <div class="ct">
    <a class="tit" href="http://www.segye.com/content/html/2017/05/27/20170527001053.html?OutUrl=naver" onclick="" target="_blank">'<b>아는형님</b>' 오현경, 강호동과 28년 우정...첫 만남 장소가 '엘레베이터'</a>
    <div class="info">
    <span class="pht"></span>
    <span class="press">세계일보</span> <span class="bar"></span> <span class="time">17시간전</span> <span class="bar"></span> <a class="go_naver" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=106&amp;oid=022&amp;aid=0003177041" onclick="" target="_blank">네이버뉴스</a>
    <!-- 개인화플러그인 -->
    <div class="head_social share_area">
    <span class="bar"></span>
    <a class="btn_share btn_social naver-splugin nclicks(STA.share)" data-mail-display="off" data-me-display="off" data-oninitialize="splugin_oninitialize('this');" data-option="{baseElement:'spiButton6', layerPosition:'outside-bottom', align:'right', top:4, left:0, marginLeft:8, marginTop:10}" data-service-name="뉴스" data-source-name="세계일보" data-style="unity-v2" data-title="'아는형님' 오현경, 강호동과 28년 우정...첫 만남 장소가 '엘레베이터'" data-url="http://www.segye.com/content/html/2017/05/27/20170527001053.html?OutUrl=naver" href="#" id="spiButton6" title="소셜공유하기">
    <span class="blind naver-splugin-c">소셜공유하기</span>
    </a>
    </div>
    <!-- 개인화플러그인 -->
    </div>
    <p class="dsc">
    				27일 방송된 JTBC '아는 형님'에서 오현경과 딘딘이 게스트로 출연했다. 이날 강호동은 오현경이 등장하자 반가워하며 "오현경과 친구가 된 진 25년이고, 알고 지낸건 28년이다"라며 "89년에 현경이는 미스코리아 진이 됐고 나는 백두장사가 됐다"라고 이야기했다. 이어서 "한 신문사에... 
    			</p>
    <!-- [D] 관련 뉴스 -->
    <div class="related_group">
    <strong class="blind">관련 뉴스</strong>
    <ul class="related_lst">
    </ul>
    </div>
    <ul class="srch_lst">
    <li>
    <a class="thmb" href="http://www.hankookilbo.com/v/843e9372f6024d558bb5d26fc6e55ad6">
    <img onerror='javascript:this.src="http://static.news.naver.net/image/news/2009/press/empty.png"; document.getElementById("image_list_469_0000204286").removeAttribute("class");' src="http://imgnews.naver.net/image/thumb80/469/2017/05/22/204286.jpg"/><span class="vm"></span>
    </a>
    <div class="ct">
    <a class="tit" href="http://www.hankookilbo.com/v/843e9372f6024d558bb5d26fc6e55ad6" onclick="" target="_blank">‘아는 형님’ 속 아재들이 야속한 까닭</a>
    <div class="info">
    <span class="pht"></span>
    <span class="press">한국일보</span> <span class="bar"></span> <span class="time">6일전</span> <span class="bar"></span> <a class="go_naver" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=106&amp;oid=469&amp;aid=0000204286" onclick="" target="_blank">네이버뉴스</a>
    <!-- 개인화플러그인 -->
    <div class="head_social share_area">
    <span class="bar"></span>
    <a class="btn_share btn_social naver-splugin nclicks(STA.share)" data-mail-display="off" data-me-display="off" data-oninitialize="splugin_oninitialize('this');" data-option="{baseElement:'spiButton7', layerPosition:'outside-bottom', align:'right', top:4, left:0, marginLeft:8, marginTop:10}" data-service-name="뉴스" data-source-name="한국일보" data-style="unity-v2" data-title="‘아는 형님’ 속 아재들이 야속한 까닭" data-url="http://www.hankookilbo.com/v/843e9372f6024d558bb5d26fc6e55ad6" href="#" id="spiButton7" title="소셜공유하기">
    <span class="blind naver-splugin-c">소셜공유하기</span>
    </a>
    </div>
    <!-- 개인화플러그인 -->
    </div>
    <p class="dsc">
    				예능으로 옮겨 놓은 마초 세상 '아는 형님' 트와이스 편의 한 장면. jtbc 화면 캡처 “제 일본 이름은 미나토자키 사나예요.”(트와이스 사나) “뭐? 민화투?” (이수근) 지난주 ‘아는 형님(JTBC)’ 트와이스 편에 등장한 대화의 일부다. ‘아는 형님’의 웃음 코드가 대부분 이런 식이다.... 
    			</p>
    <!-- [D] 관련 뉴스 -->
    <div class="related_group">
    <strong class="blind">관련 뉴스</strong>
    <ul class="related_lst">
    </ul>
    </div>
    <ul class="srch_lst">
    <li>
    <a class="thmb" href="http://en.seoul.co.kr/news/newsView.php?id=20170520500055&amp;wlog_tag3=naver">
    <img onerror='javascript:this.src="http://static.news.naver.net/image/news/2009/press/empty.png"; document.getElementById("image_list_081_0002822674").removeAttribute("class");' src="http://imgnews.naver.net/image/thumb80/081/2017/05/21/2822674.jpg"/><span class="vm"></span>
    </a>
    <div class="ct">
    <a class="tit" href="http://en.seoul.co.kr/news/newsView.php?id=20170520500055&amp;wlog_tag3=naver" onclick="" target="_blank">‘아는 형님’ 트와이스 쯔위, 김영철과 짝꿍 회상 “내가 불쌍해 보였다”</a>
    <div class="info">
    <span class="pht"></span>
    <span class="press">서울신문</span> <span class="bar"></span> <span class="time">7일전</span> <span class="bar"></span> <a class="go_naver" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=106&amp;oid=081&amp;aid=0002822674" onclick="" target="_blank">네이버뉴스</a>
    <!-- 개인화플러그인 -->
    <div class="head_social share_area">
    <span class="bar"></span>
    <a class="btn_share btn_social naver-splugin nclicks(STA.share)" data-mail-display="off" data-me-display="off" data-oninitialize="splugin_oninitialize('this');" data-option="{baseElement:'spiButton8', layerPosition:'outside-bottom', align:'right', top:4, left:0, marginLeft:8, marginTop:10}" data-service-name="뉴스" data-source-name="서울신문" data-style="unity-v2" data-title="‘아는 형님’ 트와이스 쯔위, 김영철과 짝꿍 회상 “내가 불쌍해 보였다”" data-url="http://en.seoul.co.kr/news/newsView.php?id=20170520500055&amp;wlog_tag3=naver" href="#" id="spiButton8" title="소셜공유하기">
    <span class="blind naver-splugin-c">소셜공유하기</span>
    </a>
    </div>
    <!-- 개인화플러그인 -->
    </div>
    <p class="dsc">
    				[서울신문 En] ‘아는 형님’ 트와이스 쯔위 트와이스 쯔위가 ‘아는 형님’ 김영철과의 짝꿍 시절을 회상했다. 20일 방송된 JTBC ‘<b>아는형님</b>’에는 최근 신곡 ‘시그널’로 컴백한 걸그룹 트와이스 멤버 전원이 게스트로 출연했다. 트와이스는 앞서 지난해 6월 ‘아는 형님’을 찾아... 
    			</p>
    <!-- [D] 관련 뉴스 -->
    <div class="related_group">
    <strong class="blind">관련 뉴스</strong>
    <ul class="related_lst">
    </ul>
    </div>
    <ul class="srch_lst">
    <li>
    <a class="thmb" href="http://news.chosun.com/site/data/html_dir/2017/05/20/2017052001809.html">
    <img onerror='javascript:this.src="http://static.news.naver.net/image/news/2009/press/empty.png"; document.getElementById("image_list_023_0003281769").removeAttribute("class");' src="http://imgnews.naver.net/image/thumb80/023/2017/05/21/3281769.jpg"/><span class="vm"></span>
    </a>
    <div class="ct">
    <a class="tit" href="http://news.chosun.com/site/data/html_dir/2017/05/20/2017052001809.html" onclick="" target="_blank">오현경, '아는 형님'도 숨막힌 뒤태</a>
    <div class="info">
    <span class="pht"></span>
    <span class="press">조선일보</span> <span class="bar"></span> <span class="time">7일전</span> <span class="bar"></span> <a class="go_naver" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=004&amp;oid=023&amp;aid=0003281769" onclick="" target="_blank">네이버뉴스</a>
    <!-- 개인화플러그인 -->
    <div class="head_social share_area">
    <span class="bar"></span>
    <a class="btn_share btn_social naver-splugin nclicks(STA.share)" data-mail-display="off" data-me-display="off" data-oninitialize="splugin_oninitialize('this');" data-option="{baseElement:'spiButton9', layerPosition:'outside-bottom', align:'right', top:4, left:0, marginLeft:8, marginTop:10}" data-service-name="뉴스" data-source-name="조선일보" data-style="unity-v2" data-title="오현경, '아는 형님'도 숨막힌 뒤태" data-url="http://news.chosun.com/site/data/html_dir/2017/05/20/2017052001809.html" href="#" id="spiButton9" title="소셜공유하기">
    <span class="blind naver-splugin-c">소셜공유하기</span>
    </a>
    </div>
    <!-- 개인화플러그인 -->
    </div>
    <p class="dsc">
    				/오현경 인스타그램 오현경은 또 배우 정시아와 함께 베트남 다낭에서 찍은 사진을 공개했다. 사진 속에서 두 사람은 다정한 모습으로 친분을 과시하는 한편, 동안 미모를 경쟁했다. 오현경은 지난 20일 JTBC 예능 '<b>아는형님</b>'에 출연한바 있다.  [조선닷컴 바로가기]
    			</p>
    <!-- [D] 관련 뉴스 -->
    <div class="related_group">
    <strong class="blind">관련 뉴스</strong>
    <ul class="related_lst">
    </ul>
    </div>
    </div>
    <div class="paging">
    <strong>1</strong>
    <a href="/main/search/search.nhn?query=%BE%C6%B4%C2%C7%FC%B4%D4&amp;st=news.all&amp;q_enc=EUC-KR&amp;r_enc=UTF-8&amp;r_format=xml&amp;rp=none&amp;sm=all.basic&amp;ic=all&amp;so=rel.dsc&amp;rcnews=exist:032:005:086:020:021:081:022:023:025:028:038:469:&amp;rcsection=exist:&amp;stDate=range:20170521:20170528&amp;detail=0&amp;pd=3&amp;r_cluster2_start=1&amp;r_cluster2_display=10&amp;start=1&amp;display=10&amp;startDate=2017-05-21&amp;endDate=2017-05-28&amp;page=2">2</a>
    <a href="/main/search/search.nhn?query=%BE%C6%B4%C2%C7%FC%B4%D4&amp;st=news.all&amp;q_enc=EUC-KR&amp;r_enc=UTF-8&amp;r_format=xml&amp;rp=none&amp;sm=all.basic&amp;ic=all&amp;so=rel.dsc&amp;rcnews=exist:032:005:086:020:021:081:022:023:025:028:038:469:&amp;rcsection=exist:&amp;stDate=range:20170521:20170528&amp;detail=0&amp;pd=3&amp;r_cluster2_start=1&amp;r_cluster2_display=10&amp;start=1&amp;display=10&amp;startDate=2017-05-21&amp;endDate=2017-05-28&amp;page=3">3</a>
    <a href="/main/search/search.nhn?query=%BE%C6%B4%C2%C7%FC%B4%D4&amp;st=news.all&amp;q_enc=EUC-KR&amp;r_enc=UTF-8&amp;r_format=xml&amp;rp=none&amp;sm=all.basic&amp;ic=all&amp;so=rel.dsc&amp;rcnews=exist:032:005:086:020:021:081:022:023:025:028:038:469:&amp;rcsection=exist:&amp;stDate=range:20170521:20170528&amp;detail=0&amp;pd=3&amp;r_cluster2_start=1&amp;r_cluster2_display=10&amp;start=1&amp;display=10&amp;startDate=2017-05-21&amp;endDate=2017-05-28&amp;page=4">4</a>
    </div>
    <!-- 검색결과 개수 제한 안내문구  -->
    <!-- //검색결과 개수 제한 안내문구  -->
    <!-- 관련/인기 검색어  -->
    <!-- //관련/인기 검색어  -->
    </li></ul></div>
    </li></ul></div></li></ul></div></li></ul></div></li></ul></div></li></ul></div></li></ul></div></li></ul></div></li></ul></div></li></ul></div></div></td>
    <td class="aside">
    <div class="aside">
    <div class="da">
    <div id="da_280240" name="da_280240"><iframe align="center" border="0" data-veta-preview="news_home_1" frameborder="0" height="240" id="f280240" marginheight="0" marginwidth="0" name="f280240" scrolling="no" src="http://ad.naver.com/fxshow?su=SU10001&amp;subject=sid1-104" title="배너광고" width="280"></iframe></div>
    </div>
    <div class="section section_wide">
    <h4><a class="nclicks(rig.ranking)" href="/main/ranking/popularDay.nhn">가장 많이 본 뉴스</a></h4>
    <div class="category">
    <div id="right.ranking_category_a" style="display:none">
    <span class="cate_a">
    <a class="tab1 nclicks(rig.ranking)" id="right.ranking_tab_000">종합</a>
    <a class="tab2 nclicks(rig.ranking)" id="right.ranking_tab_100">정치</a>
    <a class="tab3 nclicks(rig.ranking)" id="right.ranking_tab_101">경제</a>
    <a class="tab4 nclicks(rig.ranking)" id="right.ranking_tab_102">사회</a>
    <a class="tab5 nclicks(rig.ranking)" id="right.ranking_tab_103">생활/문화</a>
    </span>
    </div>
    <div id="right.ranking_category_b" style="display:none">
    <span class="cate_b">
    <a class="tab1 nclicks(rig.ranking)" id="right.ranking_tab_104">세계</a>
    <a class="tab2 nclicks(rig.ranking)" id="right.ranking_tab_105">IT/과학</a>
    <a class="tab3 nclicks(rig.ranking)" id="right.ranking_tab_106">TV연예</a>
    <a class="tab4 nclicks(rig.ranking)" id="right.ranking_tab_107">스포츠</a>
    </span>
    </div>
    <a class="btn_prev nclicks(rig.ranking)" id="right.ranking_btn_prev"><span class="blind">이전</span></a>
    <a class="btn_next nclicks(rig.ranking)" id="right.ranking_btn_next"><span class="blind">다음</span></a>
    </div>
    <div id="right.ranking_contents"></div>
    <div id="ranking_000" style="display:none">
    <h5 class="blind">종합</h5>
    <ul class="type14">
    <li><a class="fs11 nclicks(rig.ranking)" href="/main/ranking/popularDay.nhn?rankingType=popular_day&amp;sectionId=100">[<span>정치</span>]</a> <a class="sm2 nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=079&amp;aid=0002971631&amp;date=20170528&amp;type=1&amp;rankingSeq=1&amp;rankingSectionId=100" title='靑 "朴 탄핵 때 사용 특수활동비 30억 혼자 안 썼다"'>靑 "朴 탄핵 때 사용 특수활동비 30억 혼자 안 썼다"</a></li>
    <li><a class="fs11 nclicks(rig.ranking)" href="/main/ranking/popularDay.nhn?rankingType=popular_day&amp;sectionId=101">[<span>경제</span>]</a> <a class="sm2 nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=001&amp;aid=0009296452&amp;date=20170528&amp;type=1&amp;rankingSeq=1&amp;rankingSectionId=101" title="서울 아파트 시장 '이상 과열 조짐' 왜 이러나">서울 아파트 시장 '이상 과열 조짐' 왜 이러나</a></li>
    <li><a class="fs11 nclicks(rig.ranking)" href="/main/ranking/popularDay.nhn?rankingType=popular_day&amp;sectionId=102">[<span>사회</span>]</a> <a class="sm2 nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=001&amp;aid=0009296647&amp;date=20170528&amp;type=1&amp;rankingSeq=1&amp;rankingSectionId=102" title="주민번호 뒷자리 30일부터 변경 가능…성별은 제외">주민번호 뒷자리 30일부터 변경 가능…성별은 제외</a></li>
    <li><a class="fs11 nclicks(rig.ranking)" href="/main/ranking/popularDay.nhn?rankingType=popular_day&amp;sectionId=103">[<span>생활/문화</span>]</a> <a class="sm4 nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=001&amp;aid=0009296289&amp;date=20170528&amp;type=1&amp;rankingSeq=1&amp;rankingSectionId=103" title='"잦은 소화불량·몸살 있으면 담석 의심해봐야"'>"잦은 소화불량·몸살 있으면 담석 의심해봐야"</a></li>
    <li><a class="fs11 nclicks(rig.ranking)" href="/main/ranking/popularDay.nhn?rankingType=popular_day&amp;sectionId=104">[<span>세계</span>]</a> <a class="sm2 nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=001&amp;aid=0009296469&amp;date=20170528&amp;type=1&amp;rankingSeq=1&amp;rankingSectionId=104" title="필리핀 계엄령 소도시 교전 지속…두테르테, 반군에 대화제의">필리핀 계엄령 소도시 교전 지속…두테르테, 반군에 대화제의</a></li>
    <li><a class="fs11 nclicks(rig.ranking)" href="/main/ranking/popularDay.nhn?rankingType=popular_day&amp;sectionId=105">[<span>IT/과학</span>]</a> <a class="sm4 nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=366&amp;aid=0000370854&amp;date=20170528&amp;type=1&amp;rankingSeq=1&amp;rankingSectionId=105" title='"코딩만 배우지 마세요"…코딩 열풍 속 회의론도 제기'>"코딩만 배우지 마세요"…코딩 열풍 속 회의론도 제기</a></li>
    <li><a class="fs11 nclicks(rig.ranking)" href="/main/ranking/popularDay.nhn?rankingType=popular_day&amp;sectionId=106">[<span>연예</span>]</a> <a class="sm2 nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=213&amp;aid=0000967125&amp;date=20170528&amp;type=1&amp;rankingSeq=1&amp;rankingSectionId=106" title='[직격인터뷰] 이파니 "남편 서성민, 운명의 남자..저 정말 열심히 살게요"'>[직격인터뷰] 이파니 "남편 서성민, 운명의 남자..저 정말 열심히 살게요"</a></li>
    <li><a class="fs11 nclicks(rig.ranking)" href="/main/ranking/popularDay.nhn?rankingType=popular_day&amp;sectionId=107">[<span>스포츠</span>]</a> <a class="sm3 nclicks(rig.ranking)" href="http://sports.news.naver.com/news.nhn?oid=109&amp;aid=0003545531" title="오승환, COL전 14일만에 11SV…ML 통산 30SV">오승환, COL전 14일만에 11SV…ML 통산 30SV</a></li>
    </ul>
    </div>
    <div id="ranking_100" style="display:none">
    <h5 class="blind">정치</h5>
    <ul class="type16">
    <li><span class="rank"><em>1</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=079&amp;aid=0002971631&amp;date=20170528&amp;type=1&amp;rankingSeq=1&amp;rankingSectionId=100" title='靑 "朴 탄핵 때 사용 특수활동비 30억 혼자 안 썼다"'>靑 "朴 탄핵 때 사용 특수활동비 30억 혼자 안 썼다"</a></li>
    <li><span class="rank"><em>2</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=421&amp;aid=0002755777&amp;date=20170528&amp;type=1&amp;rankingSeq=2&amp;rankingSectionId=100" title="&quot;이낙연도 못 피했다&quot;…文정부서도 이어진 첫 총리 '수난사'">"이낙연도 못 피했다"…文정부서도 이어진 첫 총리 '수난사'</a></li>
    <li><span class="rank"><em>3</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=079&amp;aid=0002971630&amp;date=20170528&amp;type=1&amp;rankingSeq=3&amp;rankingSectionId=100" title="靑, 오후 상황점검회의 통해 야당 설득…이르면 오늘 차관 인사">靑, 오후 상황점검회의 통해 야당 설득…이르면 오늘 차관 인사</a></li>
    <li><span class="rank"><em>4</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=001&amp;aid=0009296395&amp;date=20170528&amp;type=1&amp;rankingSeq=4&amp;rankingSectionId=100" title="'돈봉투 만찬' 감찰팀, 문제식당서 현장조사하며 식사(종합)">'돈봉투 만찬' 감찰팀, 문제식당서 현장조사하며 식사(종합)</a></li>
    <li><span class="rank"><em>5</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=001&amp;aid=0009296586&amp;date=20170528&amp;type=1&amp;rankingSeq=5&amp;rankingSectionId=100" title='우원식 "野, 대승적 인준 호소…공직자 검증기준 함께 만들자"(종합)'>우원식 "野, 대승적 인준 호소…공직자 검증기준 함께 만들자"(종합)</a></li>
    <li><span class="rank"><em>6</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=421&amp;aid=0002755846&amp;date=20170528&amp;type=1&amp;rankingSeq=6&amp;rankingSectionId=100" title="&quot;이산가족들이 다시 모였습니다&quot;…文대통령 'SNS 소통정치'">"이산가족들이 다시 모였습니다"…文대통령 'SNS 소통정치'</a></li>
    <li><span class="rank"><em>7</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=001&amp;aid=0009296841&amp;date=20170528&amp;type=1&amp;rankingSeq=7&amp;rankingSectionId=100" title='국정委 "고위공직자 임용기준안과 청문회 개선방안 마련할 것"'>국정委 "고위공직자 임용기준안과 청문회 개선방안 마련할 것"</a></li>
    <li><span class="rank"><em>8</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=421&amp;aid=0002755815&amp;date=20170528&amp;type=1&amp;rankingSeq=8&amp;rankingSectionId=100" title="이번주 文정부 1기내각 청문회 줄줄이 열려…국정운영 분수령">이번주 文정부 1기내각 청문회 줄줄이 열려…국정운영 분수령</a></li>
    </ul>
    </div>
    <div id="ranking_101" style="display:none">
    <h5 class="blind">경제</h5>
    <ul class="type16">
    <li><span class="rank"><em>1</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=001&amp;aid=0009296452&amp;date=20170528&amp;type=1&amp;rankingSeq=1&amp;rankingSectionId=101" title="서울 아파트 시장 '이상 과열 조짐' 왜 이러나">서울 아파트 시장 '이상 과열 조짐' 왜 이러나</a></li>
    <li><span class="rank"><em>2</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=003&amp;aid=0007980081&amp;date=20170528&amp;type=1&amp;rankingSeq=2&amp;rankingSectionId=101" title="'홀인원' 너무 많다 했더니…골프 보험사기 140명 무더기 적발">'홀인원' 너무 많다 했더니…골프 보험사기 140명 무더기 적발</a></li>
    <li><span class="rank"><em>3</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=421&amp;aid=0002755552&amp;date=20170528&amp;type=1&amp;rankingSeq=3&amp;rankingSectionId=101" title='"100:1 경쟁률 뚫었는데…" 금융권 정규직 전환 설왕설래'>"100:1 경쟁률 뚫었는데…" 금융권 정규직 전환 설왕설래</a></li>
    <li><span class="rank"><em>4</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=421&amp;aid=0002755520&amp;date=20170528&amp;type=1&amp;rankingSeq=4&amp;rankingSectionId=101" title='[서민물가 너무해②]"안 오른게 없어요" 고달픈 서민'>[서민물가 너무해②]"안 오른게 없어요" 고달픈 서민</a></li>
    <li><span class="rank"><em>5</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=422&amp;aid=0000258416&amp;date=20170528&amp;type=2&amp;rankingSeq=5&amp;rankingSectionId=101" title="경기 회복 조짐?…비싼 신차 팔리고 분양 늘어">경기 회복 조짐?…비싼 신차 팔리고 분양 늘어</a></li>
    <li><span class="rank"><em>6</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=421&amp;aid=0002755864&amp;date=20170528&amp;type=1&amp;rankingSeq=6&amp;rankingSectionId=101" title="1조 돌파한 P2P 대출…투자 한도 '연 1천만원'으로 제한">1조 돌파한 P2P 대출…투자 한도 '연 1천만원'으로 제한</a></li>
    <li><span class="rank"><em>7</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=015&amp;aid=0003774406&amp;date=20170528&amp;type=1&amp;rankingSeq=7&amp;rankingSectionId=101" title="단짝 친구와 동업…월 순익 800만원, 경쟁 덜한 골목상권 소형점포 '주효'">단짝 친구와 동업…월 순익 800만원, 경쟁 덜한 골목상권 소형점포 '주효'</a></li>
    <li><span class="rank"><em>8</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=079&amp;aid=0002971648&amp;date=20170528&amp;type=1&amp;rankingSeq=8&amp;rankingSectionId=101" title="4대강 실행 주역 김동연, 인사청문회 넘어설까">4대강 실행 주역 김동연, 인사청문회 넘어설까</a></li>
    </ul>
    </div>
    <div id="ranking_102" style="display:none">
    <h5 class="blind">사회</h5>
    <ul class="type16">
    <li><span class="rank"><em>1</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=001&amp;aid=0009296647&amp;date=20170528&amp;type=1&amp;rankingSeq=1&amp;rankingSectionId=102" title="주민번호 뒷자리 30일부터 변경 가능…성별은 제외">주민번호 뒷자리 30일부터 변경 가능…성별은 제외</a></li>
    <li><span class="rank"><em>2</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=003&amp;aid=0007979967&amp;date=20170528&amp;type=1&amp;rankingSeq=2&amp;rankingSectionId=102" title='"왜 느리게 가?" 앞차 들이받고 운전자 폭행한 30대'>"왜 느리게 가?" 앞차 들이받고 운전자 폭행한 30대</a></li>
    <li><span class="rank"><em>3</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=028&amp;aid=0002366230&amp;date=20170528&amp;type=1&amp;rankingSeq=3&amp;rankingSectionId=102" title=" 손주들 생각하며 어떻게든 내 선에서 막아야지 "> 손주들 생각하며 어떻게든 내 선에서 막아야지 </a></li>
    <li><span class="rank"><em>4</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=008&amp;aid=0003879171&amp;date=20170528&amp;type=1&amp;rankingSeq=4&amp;rankingSectionId=102" title="서울 일선서 경찰, 경기 수원 야산서 숨친 채 발견">서울 일선서 경찰, 경기 수원 야산서 숨친 채 발견</a></li>
    <li><span class="rank"><em>5</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=001&amp;aid=0009297100&amp;date=20170528&amp;type=1&amp;rankingSeq=5&amp;rankingSectionId=102" title="개 사육장서 60대女 도사견에 물려 숨져…남편도 상처입어(종합)">개 사육장서 60대女 도사견에 물려 숨져…남편도 상처입어(종합)</a></li>
    <li><span class="rank"><em>6</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=001&amp;aid=0009297051&amp;date=20170528&amp;type=1&amp;rankingSeq=6&amp;rankingSectionId=102" title="동료여경 해킹 약점 잡아 1천만원 뜯은 경찰 구속기소">동료여경 해킹 약점 잡아 1천만원 뜯은 경찰 구속기소</a></li>
    <li><span class="rank"><em>7</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=022&amp;aid=0003177113&amp;date=20170528&amp;type=1&amp;rankingSeq=7&amp;rankingSectionId=102" title='[이슈&amp;현장] "세월호 이후에도 달라진 게 없다" 스텔라데이지호 실종자 가족의 절규'>[이슈&amp;현장] "세월호 이후에도 달라진 게 없다" 스텔라데이지호 실종자 가족의 절규</a></li>
    <li><span class="rank"><em>8</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=001&amp;aid=0009296363&amp;date=20170528&amp;type=1&amp;rankingSeq=8&amp;rankingSectionId=102" title="'구의역 사고' 꼭 1년만에 서울메트로 등 책임자 9명 재판">'구의역 사고' 꼭 1년만에 서울메트로 등 책임자 9명 재판</a></li>
    </ul>
    </div>
    <div id="ranking_103" style="display:none">
    <h5 class="blind">생활/문화</h5>
    <ul class="type16">
    <li><span class="rank"><em>1</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=001&amp;aid=0009296289&amp;date=20170528&amp;type=1&amp;rankingSeq=1&amp;rankingSectionId=103" title='"잦은 소화불량·몸살 있으면 담석 의심해봐야"'>"잦은 소화불량·몸살 있으면 담석 의심해봐야"</a></li>
    <li><span class="rank"><em>2</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=422&amp;aid=0000258409&amp;date=20170528&amp;type=2&amp;rankingSeq=2&amp;rankingSectionId=103" title="[날씨] 전국 낮 30도 초여름…자외선ㆍ오존 주의">[날씨] 전국 낮 30도 초여름…자외선ㆍ오존 주의</a></li>
    <li><span class="rank"><em>3</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=003&amp;aid=0007980342&amp;date=20170528&amp;type=1&amp;rankingSeq=3&amp;rankingSectionId=103" title="발 디딜 틈 없는 해운대해수욕장">발 디딜 틈 없는 해운대해수욕장</a></li>
    <li><span class="rank"><em>4</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=028&amp;aid=0002366248&amp;date=20170528&amp;type=1&amp;rankingSeq=4&amp;rankingSectionId=103" title=" 풍납토성에 레미콘 공장 허용…학계 “법원이 사적 파괴 조장” "> 풍납토성에 레미콘 공장 허용…학계 “법원이 사적 파괴 조장” </a></li>
    <li><span class="rank"><em>5</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=001&amp;aid=0009296274&amp;date=20170528&amp;type=1&amp;rankingSeq=5&amp;rankingSectionId=103" title="[제주 돌문화] ① 돌 틈에서 나고 자라 돌 틈으로 돌아가다">[제주 돌문화] ① 돌 틈에서 나고 자라 돌 틈으로 돌아가다</a></li>
    <li><span class="rank"><em>6</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=353&amp;aid=0000027070&amp;date=20170528&amp;type=1&amp;rankingSeq=6&amp;rankingSectionId=103" title="한바탕 놀다가는 세상, 희망가를 불러야쥬">한바탕 놀다가는 세상, 희망가를 불러야쥬</a></li>
    <li><span class="rank"><em>7</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=421&amp;aid=0002755890&amp;date=20170528&amp;type=1&amp;rankingSeq=7&amp;rankingSectionId=103" title="&quot;중국 관광객 7월에 온다&quot; 전망…관광업계 '아직은 글쎄'">"중국 관광객 7월에 온다" 전망…관광업계 '아직은 글쎄'</a></li>
    <li><span class="rank"><em>8</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=001&amp;aid=0009296796&amp;date=20170528&amp;type=1&amp;rankingSeq=8&amp;rankingSectionId=103" title='"19세기말 소설 대여점은 문학의 산실"…日교토대 한글소설 분석'>"19세기말 소설 대여점은 문학의 산실"…日교토대 한글소설 분석</a></li>
    </ul>
    </div>
    <div id="ranking_104" style="display:none">
    <h5 class="blind">세계</h5>
    <ul class="type16">
    <li><span class="rank"><em>1</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=001&amp;aid=0009296469&amp;date=20170528&amp;type=1&amp;rankingSeq=1&amp;rankingSectionId=104" title="필리핀 계엄령 소도시 교전 지속…두테르테, 반군에 대화제의">필리핀 계엄령 소도시 교전 지속…두테르테, 반군에 대화제의</a></li>
    <li><span class="rank"><em>2</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=018&amp;aid=0003835847&amp;date=20170528&amp;type=1&amp;rankingSeq=2&amp;rankingSectionId=104" title="G7정상회의, '역대 최악' 분위기속 폐막…트럼프가 몰고 온 분열">G7정상회의, '역대 최악' 분위기속 폐막…트럼프가 몰고 온 분열</a></li>
    <li><span class="rank"><em>3</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=003&amp;aid=0007979964&amp;date=20170528&amp;type=1&amp;rankingSeq=3&amp;rankingSectionId=104" title="영국 경찰, 맨체스터 테러범 범행 직전 CCTV 장면 공개">영국 경찰, 맨체스터 테러범 범행 직전 CCTV 장면 공개</a></li>
    <li><span class="rank"><em>4</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=001&amp;aid=0009296827&amp;date=20170528&amp;type=1&amp;rankingSeq=4&amp;rankingSectionId=104" title="'묶인 채' 살해된 8명포함 필리핀 민간인 19명 '처참한 죽음'">'묶인 채' 살해된 8명포함 필리핀 민간인 19명 '처참한 죽음'</a></li>
    <li><span class="rank"><em>5</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=003&amp;aid=0007979885&amp;date=20170528&amp;type=1&amp;rankingSeq=5&amp;rankingSectionId=104" title="중국, G7 정상선언 '동·남중국해 우려'에 강력히 반발">중국, G7 정상선언 '동·남중국해 우려'에 강력히 반발</a></li>
    <li><span class="rank"><em>6</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=421&amp;aid=0002755674&amp;date=20170528&amp;type=1&amp;rankingSeq=6&amp;rankingSectionId=104" title="러 스캔들 몸통 쿠슈너가 만난 은행가는 '푸틴 친구'">러 스캔들 몸통 쿠슈너가 만난 은행가는 '푸틴 친구'</a></li>
    <li><span class="rank"><em>7</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=421&amp;aid=0002756163&amp;date=20170528&amp;type=1&amp;rankingSeq=7&amp;rankingSectionId=104" title="유럽서 퍼지는 '안티 백신'…獨정부 &quot;안 맞추면 벌금&quot;">유럽서 퍼지는 '안티 백신'…獨정부 "안 맞추면 벌금"</a></li>
    <li><span class="rank"><em>8</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=022&amp;aid=0003177076&amp;date=20170528&amp;type=1&amp;rankingSeq=8&amp;rankingSectionId=104" title="먹이 줘야 관광객 소지품 돌려줘…발리의 '마피아 원숭이' 화제">먹이 줘야 관광객 소지품 돌려줘…발리의 '마피아 원숭이' 화제</a></li>
    </ul>
    </div>
    <div id="ranking_105" style="display:none">
    <h5 class="blind">IT/과학</h5>
    <ul class="type16">
    <li><span class="rank"><em>1</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=366&amp;aid=0000370854&amp;date=20170528&amp;type=1&amp;rankingSeq=1&amp;rankingSectionId=105" title='"코딩만 배우지 마세요"…코딩 열풍 속 회의론도 제기'>"코딩만 배우지 마세요"…코딩 열풍 속 회의론도 제기</a></li>
    <li><span class="rank"><em>2</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=003&amp;aid=0007980222&amp;date=20170528&amp;type=1&amp;rankingSeq=2&amp;rankingSectionId=105" title="갤럭시S8, 국내 개통량 100만대 돌파…전작 비해 2배 빨라">갤럭시S8, 국내 개통량 100만대 돌파…전작 비해 2배 빨라</a></li>
    <li><span class="rank"><em>3</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=001&amp;aid=0009296412&amp;date=20170528&amp;type=1&amp;rankingSeq=3&amp;rankingSectionId=105" title="감정노동 논란 '사랑합니다, 고객님' 114 인사말 부활하나">감정노동 논란 '사랑합니다, 고객님' 114 인사말 부활하나</a></li>
    <li><span class="rank"><em>4</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=421&amp;aid=0002755832&amp;date=20170528&amp;type=1&amp;rankingSeq=4&amp;rankingSectionId=105" title="전세계 강타한 '워너크라이 랜섬웨어', 韓 피해 적었던 이유는 ">전세계 강타한 '워너크라이 랜섬웨어', 韓 피해 적었던 이유는 </a></li>
    <li><span class="rank"><em>5</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=001&amp;aid=0009296313&amp;date=20170528&amp;type=1&amp;rankingSeq=5&amp;rankingSectionId=105" title="'바둑 정복' 알파고, 이제 의료·과학 분야 무한도전 나선다">'바둑 정복' 알파고, 이제 의료·과학 분야 무한도전 나선다</a></li>
    <li><span class="rank"><em>6</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=030&amp;aid=0002612467&amp;date=20170528&amp;type=1&amp;rankingSeq=6&amp;rankingSectionId=105" title="이베이코리아, '옵션가격' 꼼수없앤다...&quot;원 아이템-원 리스트&quot;">이베이코리아, '옵션가격' 꼼수없앤다..."원 아이템-원 리스트"</a></li>
    <li><span class="rank"><em>7</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=277&amp;aid=0004002829&amp;date=20170528&amp;type=1&amp;rankingSeq=7&amp;rankingSectionId=105" title="신기록 제조기 '갤럭시S8' 국내 개통량 100만대 돌파(종합)">신기록 제조기 '갤럭시S8' 국내 개통량 100만대 돌파(종합)</a></li>
    <li><span class="rank"><em>8</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=014&amp;aid=0003821063&amp;date=20170528&amp;type=1&amp;rankingSeq=8&amp;rankingSectionId=105" title="[르포]SW교육 의무화 코앞...코딩배우기 열풍 현장을 가다">[르포]SW교육 의무화 코앞...코딩배우기 열풍 현장을 가다</a></li>
    </ul>
    </div>
    <div id="ranking_106" style="display:none">
    <h5 class="blind">연예</h5>
    <ul class="type16">
    <li><span class="rank"><em>1</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=213&amp;aid=0000967125&amp;date=20170528&amp;type=1&amp;rankingSeq=1&amp;rankingSectionId=106" title='[직격인터뷰] 이파니 "남편 서성민, 운명의 남자..저 정말 열심히 살게요"'>[직격인터뷰] 이파니 "남편 서성민, 운명의 남자..저 정말 열심히 살게요"</a></li>
    <li><span class="rank"><em>2</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=001&amp;aid=0009296415&amp;date=20170528&amp;type=1&amp;rankingSeq=2&amp;rankingSectionId=106" title="'프듀101' 과열 팬심·편집논란 '몸살'…견제투표에 팬광고까지">'프듀101' 과열 팬심·편집논란 '몸살'…견제투표에 팬광고까지</a></li>
    <li><span class="rank"><em>3</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=109&amp;aid=0003545549&amp;date=20170528&amp;type=1&amp;rankingSeq=3&amp;rankingSectionId=106" title='[Oh!커피 한 잔②] 이정재 "드라마 안하냐고? 출연 제안 많지 않아"'>[Oh!커피 한 잔②] 이정재 "드라마 안하냐고? 출연 제안 많지 않아"</a></li>
    <li><span class="rank"><em>4</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=311&amp;aid=0000738407&amp;date=20170528&amp;type=1&amp;rankingSeq=4&amp;rankingSectionId=106" title="[엑's 인터뷰] 김영철 &quot;'따르릉'으로 소원성취, 글로벌하게 터졌으면&quot;">[엑's 인터뷰] 김영철 "'따르릉'으로 소원성취, 글로벌하게 터졌으면"</a></li>
    <li><span class="rank"><em>5</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=213&amp;aid=0000967105&amp;date=20170528&amp;type=1&amp;rankingSeq=5&amp;rankingSectionId=106" title="[할리웃통신] 미란다 커♥에반 스피겔, 부부됐다…LA서 비밀리 웨딩">[할리웃통신] 미란다 커♥에반 스피겔, 부부됐다…LA서 비밀리 웨딩</a></li>
    <li><span class="rank"><em>6</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=421&amp;aid=0002755744&amp;date=20170528&amp;type=1&amp;rankingSeq=6&amp;rankingSectionId=106" title="[N1★초점] 김희철·이상민·서장훈, 어느새 예능 '중심'에">[N1★초점] 김희철·이상민·서장훈, 어느새 예능 '중심'에</a></li>
    <li><span class="rank"><em>7</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=109&amp;aid=0003545393&amp;date=20170528&amp;type=1&amp;rankingSeq=7&amp;rankingSectionId=106" title="[美친box] '캐리비안의 해적5'도 통했다..시리즈 전편 4일만 100만 돌파">[美친box] '캐리비안의 해적5'도 통했다..시리즈 전편 4일만 100만 돌파</a></li>
    <li><span class="rank"><em>8</em></span>
    <a class="nclicks(rig.ranking)" href="/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=112&amp;aid=0002924436&amp;date=20170528&amp;type=1&amp;rankingSeq=8&amp;rankingSectionId=106" title="'인가' 트와이스, 1위로 5관왕 달성..세븐틴·아이콘 컴백(종합)">'인가' 트와이스, 1위로 5관왕 달성..세븐틴·아이콘 컴백(종합)</a></li>
    </ul>
    </div>
    <div id="ranking_107" style="display:none">
    <h5 class="blind">스포츠</h5>
    <ul class="type16">
    <li><span class="rank"><em>1</em></span>
    <a class="nclicks(rig.ranking)" href="http://sports.news.naver.com/news.nhn?oid=109&amp;aid=0003545531" title="오승환, COL전 14일만에 11SV…ML 통산 30SV">오승환, COL전 14일만에 11SV…ML 통산 30SV</a></li>
    <li><span class="rank"><em>2</em></span>
    <a class="nclicks(rig.ranking)" href="http://sports.news.naver.com/news.nhn?oid=076&amp;aid=0003098738" title="'4회까지 9K' 차우찬, 역대 31번째 1000K 달성">'4회까지 9K' 차우찬, 역대 31번째 1000K 달성</a></li>
    <li><span class="rank"><em>3</em></span>
    <a class="nclicks(rig.ranking)" href="http://sports.news.naver.com/news.nhn?oid=001&amp;aid=0009296561" title="냉정하게 손 턴 알파고와 뜨거운 눈물 흘린 커제">냉정하게 손 턴 알파고와 뜨거운 눈물 흘린 커제</a></li>
    <li><span class="rank"><em>4</em></span>
    <a class="nclicks(rig.ranking)" href="http://sports.news.naver.com/news.nhn?oid=076&amp;aid=0003098667" title='코스타 "중국행 No, 이적한다면 ATM 뿐"'>코스타 "중국행 No, 이적한다면 ATM 뿐"</a></li>
    <li><span class="rank"><em>5</em></span>
    <a class="nclicks(rig.ranking)" href="http://sports.news.naver.com/news.nhn?oid=109&amp;aid=0003545480" title="KIA, 흔들리는 불펜 대폭 교체…홍건희 한승혁 박지훈 2군행 ">KIA, 흔들리는 불펜 대폭 교체…홍건희 한승혁 박지훈 2군행 </a></li>
    <li><span class="rank"><em>6</em></span>
    <a class="nclicks(rig.ranking)" href="http://sports.news.naver.com/news.nhn?oid=139&amp;aid=0002075419" title="맨유, 페리시치 영입 협상 중...이적료 516억 &lt;英 스카이스포츠&gt;">맨유, 페리시치 영입 협상 중...이적료 516억 </a></li>
    <li><span class="rank"><em>7</em></span>
    <a class="nclicks(rig.ranking)" href="http://sports.news.naver.com/news.nhn?oid=139&amp;aid=0002075435" title="'입지 불안' 이청용, &quot;아쉬웠던 시즌...내년은 더 나을 것&quot;">'입지 불안' 이청용, "아쉬웠던 시즌...내년은 더 나을 것"</a></li>
    <li><span class="rank"><em>8</em></span>
    <a class="nclicks(rig.ranking)" href="http://sports.news.naver.com/news.nhn?oid=216&amp;aid=0000089336" title='파브레가스 "FA컵 보다 프리미어리그 우승이 더 좋다"'>파브레가스 "FA컵 보다 프리미어리그 우승이 더 좋다"</a></li>
    </ul>
    </div>
    <a class="more_link nclicks(rig.ranking)" href="/main/ranking/popularDay.nhn"><img alt="가장 많이 본 뉴스 더보기" height="13" src="http://static.news.naver.net/image/news/2009/txt_more_link2.gif" width="33"/></a>
    </div>
    <script type="text/javascript">
    var ranking_selectedSectionId = '';
    var ranking_listAllSection = jindo.$A(['000', '100', '101', '102', '103', '104', '105', '106', '107']);
    var ranking_listPrevSection = ranking_listAllSection.slice(0,5);
    function ranking_switch_category(event) {
    if (jindo.$Element('right.ranking_category_a').visible()) {
    ranking_select_section('104');
    } else {
    ranking_select_section('000');
    }
    }
    jindo.$A(['prev', 'next']).forEach( function (v, i, o) {
    jindo.$Fn(ranking_switch_category, window).attach(jindo.$Element(jindo.$('right.ranking_btn_' + v)), 'click');
    });
    function ranking_select_tab(sectionId) {
    if (ranking_listPrevSection.has(sectionId)) {
    jindo.$Element('right.ranking_category_b').hide();
    jindo.$Element('right.ranking_category_a').show();
    } else {
    jindo.$Element('right.ranking_category_a').hide();
    jindo.$Element('right.ranking_category_b').show();
    }
    jindo.$('right.ranking_tab_' + sectionId).innerHTML = "<strong>" + ranking_select_section_name(sectionId) + "</strong>";
    jindo.$Element('right.ranking_tab_' + sectionId).addClass('is_click');
    if (ranking_selectedSectionId) {
    jindo.$('right.ranking_tab_' + ranking_selectedSectionId).innerHTML = ranking_select_section_name(ranking_selectedSectionId);
    jindo.$Element('right.ranking_tab_' + ranking_selectedSectionId).removeClass('is_click');
    }
    ranking_selectedSectionId = sectionId;
    }
    function ranking_select_section(sectionId) {
    if (!ranking_listAllSection.has(sectionId)) {
    sectionId = '000';
    }
    jindo.$('right.ranking_contents').innerHTML = jindo.$('ranking_' + sectionId).innerHTML;
    ranking_select_tab(sectionId);
    }
    function ranking_select_section_name(sectionId) {
    var selectSectionName = "종합";
    switch (sectionId) {
    case '100' : selectSectionName = "정치"; break;
    case '101' : selectSectionName = "경제"; break;
    case '102' : selectSectionName = "사회"; break;
    case '103' : selectSectionName = "생활/문화"; break;
    case '104' : selectSectionName = "세계"; break;
    case '105' : selectSectionName = "IT/과학"; break;
    case '106' : selectSectionName = "TV연예"; break;
    case '107' : selectSectionName = "스포츠"; break;
    default : selectSectionName = "종합" ;
    }
    return selectSectionName;
    }
    function ranking_tab_handler(event) {
    var sectionId = jindo.$Event(event).element.id.replace('right\.ranking_tab_', '');
    if (sectionId) {
    ranking_select_section(sectionId);
    }
    }
    ranking_listAllSection.forEach(function (v, i, o) {
    jindo.$Fn(ranking_tab_handler, window).attach(jindo.$Element(jindo.$('right.ranking_tab_' + v)), 'mouseover');
    });
    ranking_select_section('201');
    </script>
    <div class="section">
    <h4>핫!이슈</h4>
    <ul>
    <li class="image">
    <a class="thumb nclicks(rig.simulti)" href="http://news.naver.com/main/hotissue/sectionList.nhn?mid=hot&amp;sid1=100&amp;gid=1064085">
    <img alt="문재인 정부 출범" height="50" src="http://imgnews.naver.net/image/upload/item/2017/05/10/005657638_M_%25BC%25BD%25BC%25C7%25C8%25A8_290x210_1_%25B9%25AE%25C0%25E7%25C0%25CE.png" width="70"/>
    </a>
    <a class="nclicks(rig.simulti)" href="http://news.naver.com/main/hotissue/sectionList.nhn?mid=hot&amp;sid1=100&amp;gid=1064085">
    문재인 정부 출범
    </a>
    </li>
    <li class="image">
    <a class="thumb nclicks(rig.simulti)" href="http://news.naver.com/main/hotissue/sectionList.nhn?mid=hot&amp;sid1=102&amp;cid=984650&amp;gid=1012477">
    <img alt="세월호인양" height="50" src="http://imgnews.naver.net/image/upload/item/2017/04/21/171003992_140103399_Untitled-3.jpg" width="70"/>
    </a>
    <a class="nclicks(rig.simulti)" href="http://news.naver.com/main/hotissue/sectionList.nhn?mid=hot&amp;sid1=102&amp;cid=984650&amp;gid=1012477">
    세월호<br/>인양
    </a>
    </li>
    <li class="image">
    <a class="thumb nclicks(rig.simulti)" href="http://news.naver.com/main/hotissue/sectionList.nhn?mid=hot&amp;sid1=100&amp;gid=1051906&amp;cid=1060622">
    <img alt="박근혜 재판 · 최순실 국정농단" height="50" src="http://imgnews.naver.net/image/upload/item/2017/03/17/170741639_0002616804_001_20170317152927520.jpg" width="70"/>
    </a>
    <a class="nclicks(rig.simulti)" href="http://news.naver.com/main/hotissue/sectionList.nhn?mid=hot&amp;sid1=100&amp;gid=1051906&amp;cid=1060622">
    박근혜 재판 · <br/>최순실 국정농단
    </a>
    </li>
    <li class="image">
    <a class="thumb nclicks(rig.simulti)" href="javascript:openPhotoSlideByLinkUrl('http://news.naver.com/main/photogallery/index.nhn?cid=987213#','','');">
    <img alt="봄 여름 가을 겨울,그리고 오늘" height="50" src="http://imgnews.naver.net/image/upload/item/2017/05/19/185031006_31.jpg" width="70"/>
    <strong class="r_ico r_pho_xsmall" title="포토갤러리">포토갤러리 기사</strong><span class="trans_border"></span>
    </a>
    <a class="nclicks(rig.simulti)" href="javascript:openPhotoSlideByLinkUrl('http://news.naver.com/main/photogallery/index.nhn?cid=987213#','','');">
    봄 여름 가을 겨울,<br/>그리고 오늘
    </a>
    </li>
    <li class="image">
    <a class="thumb nclicks(rig.simulti)" href="http://news.naver.com/main/hotissue/sectionList.nhn?mid=hot&amp;sid1=104&amp;gid=1035341">
    <img alt="美 트럼프 시대" height="50" src="http://imgnews.naver.net/image/upload/item/2016/11/11/165249305_4.jpg" width="70"/>
    </a>
    <a class="nclicks(rig.simulti)" href="http://news.naver.com/main/hotissue/sectionList.nhn?mid=hot&amp;sid1=104&amp;gid=1035341">
    美 트럼프 시대
    </a>
    </li>
    </ul>
    </div>
    <div class="ombudsman section" style="margin-bottom:20px;">
    <h4>네티즌 와글와글</h4>
    <ul class="type03">
    <li class="image5">
    <a class="thumb nclicks(rig.smalltext)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=102&amp;oid=001&amp;aid=0009296647&amp;cid=512473&amp;iid=49511075"><img alt="주민번호 뒷자리 30일부터 변경 가능…성별은 제외" height="50" src="http://dthumb.phinf.naver.net/?src=http://imgnews.naver.net/image/origin/001/2017/05/28/9296647.jpg&amp;twidth=70&amp;theight=50&amp;opts=10" title="주민번호 뒷자리 30일부터 변경 가능…성별은 제외" width="70"/></a>
    <a class="nclicks(rig.smalltext)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=102&amp;oid=001&amp;aid=0009296647&amp;cid=512473&amp;iid=49511075"><strong>주민번호 뒷자리 30일부터 <br/>변경 가능…성별은 제외</strong><img alt="new" class="icon" height="8" src="http://static.news.naver.net/image/news/2009/ico_n_8x8.gif" width="8"/></a>
    </li>
    </ul>
    <ul class="type02">
    <li>
    <a class="nclicks(rig.smalltext)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=102&amp;oid=003&amp;aid=0007979967&amp;cid=512473&amp;iid=49511074">"왜 느리게 가?" 앞차 들이받고 운전자..</a>
    </li>
    <li>
    <a class="nclicks(rig.smalltext)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=101&amp;oid=421&amp;aid=0002755520&amp;cid=512473&amp;iid=49511073">서민물가 너무해.."안 오른게 없어요"</a>
    </li>
    <li>
    <a class="nclicks(rig.smalltext)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=104&amp;oid=001&amp;aid=0009296469&amp;cid=512473&amp;iid=49511072">필리핀 계엄령 소도시 교전 지속..두테르테..</a>
    </li>
    </ul>
    </div>
    <div class="section" id="right_dailyList" style="margin-bottom:20px; white-space:nowrap;">
    <h4><a class="nclicks(rig.section)" href="http://news.naver.com/main/hotissue/dailyList.nhn" title="분야별 주요뉴스">분야별 주요뉴스</a></h4>
    <div class="category">
    <span class="cate_c">
    <a class="tab1 nclicks(rig.section)">시사</a>
    <a class="tab2 nclicks(rig.section)">스포츠</a>
    <a class="tab3 nclicks(rig.section)">TV연예</a>
    <a class="tab4 nclicks(rig.section)">경제/생활</a>
    </span>
    </div>
    <div class="classfy sd" style="display:none">
    <h5 class="blind">시사</h5>
    <ul class="type02">
    <li>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=100&amp;oid=001&amp;aid=0009297168" title="'野자극할라'…文대통령, 총리 인준 앞두고 조각 속도조절">'野자극할라'…文대통령, 총리 인준 앞두고 조각 속도조절</a>
    </li>
    <li>
    <strong>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=100&amp;oid=001&amp;aid=0009297264" title="'이낙연 인준안' 내일 처리 불투명…대치정국 장기화 가능성도">'이낙연 인준안' 내일 처리 불투명…대치정국 장기화 가능성도</a>
    </strong>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=100&amp;oid=008&amp;aid=0003879211" title='서훈 국정원 후보자 "국정원 댓글 알바 사건 재수사" 시사'>서훈 국정원 후보자 "국정원 댓글 알바 사건 재수사" 시사</a>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=100&amp;oid=052&amp;aid=0001015174" title='與 "협치 요청"·野, 파상공세…청문회 정국 분수령'>與 "협치 요청"·野, 파상공세…청문회 정국 분수령</a>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=100&amp;oid=001&amp;aid=0009297057" title='국정委 "고위공직자 임용기준안·청문회 개선방안 마련할 것"(종합)'>국정委 "고위공직자 임용기준안·청문회 개선방안 마련할 것"(종합)</a>
    </li>
    </ul><ul class="type02 type02_1">
    <li>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=100&amp;oid=421&amp;aid=0002755846" title=" &quot;이산가족들이 다시 모였습니다&quot;…文대통령 'SNS 소통정치'"> "이산가족들이 다시 모였습니다"…文대통령 'SNS 소통정치'</a>
    </li>
    <li>
    <strong>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=102&amp;oid=025&amp;aid=0002720425" title="[현장에서]구의역 사고 1년, 지체된 정의 논란">[현장에서]구의역 사고 1년, 지체된 정의 논란</a>
    </strong>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=102&amp;oid=001&amp;aid=0009297173" title="'돈봉투 만찬' 감찰반, 이영렬·안태근 등 20여명 조사 완료">'돈봉투 만찬' 감찰반, 이영렬·안태근 등 20여명 조사 완료</a>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=102&amp;oid=025&amp;aid=0002720447" title='사흘 간 6600건 의견 모인 국민인수위…"무슨 의견이든.."'>사흘 간 6600건 의견 모인 국민인수위…"무슨 의견이든.."</a>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=102&amp;oid=469&amp;aid=0000205385" title='"이런 자리 생길 줄 알았으면, 한강다리 안 올라갔죠."'>"이런 자리 생길 줄 알았으면, 한강다리 안 올라갔죠."</a>
    </li>
    </ul><ul class="type02 type02_1">
    <li>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=102&amp;oid=022&amp;aid=0003177113" title='[이슈&amp;현장] "세월호 이후에도 달라진 게 없다" 스텔라데이지호 실종자 가족의 절규'>[이슈&amp;현장] "세월호 이후에도 달라진 게 없다" 스텔라데이지호 실종자 가족의 절규</a>
    </li>
    <li>
    <strong>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=104&amp;oid=003&amp;aid=0007980532" title="험악했던 G7 회의장…새벽 2시까지 회의에도 美 설득 실패">험악했던 G7 회의장…새벽 2시까지 회의에도 美 설득 실패</a>
    </strong>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=104&amp;oid=003&amp;aid=0007979964" title="영국 경찰, 맨체스터 테러범 범행 직전 CCTV 장면 공개">영국 경찰, 맨체스터 테러범 범행 직전 CCTV 장면 공개</a>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=104&amp;oid=003&amp;aid=0007979885" title="중국, G7 정상선언 '동·남중국해 우려'에 강력히 반발">중국, G7 정상선언 '동·남중국해 우려'에 강력히 반발</a>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=104&amp;oid=032&amp;aid=0002791318" title="메시지도 감동도 없었다…'무례한 악수'만 남긴 트럼프의 첫 해외순방">메시지도 감동도 없었다…'무례한 악수'만 남긴 트럼프의 첫 해외순방</a>
    </li>
    </ul>
    </div>
    <div class="classfy sd" style="display:none">
    <h5 class="blind">스포츠</h5>
    <ul class="type02">
    <li>
    <a class="nclicks(rig.section)" href="http://sports.news.naver.com/esports/news/read.nhn?oid=442&amp;aid=0000058397">태그매치 G-Toring 한수 위, 승리자 KUDETA!</a>
    </li>
    <li><strong>
    <a class="nclicks(rig.section)" href="http://sports.news.naver.com/wbaseball/news/read.nhn?oid=460&amp;aid=0000000598">[오늘의 MLB] 볼티모어 6연패, 김현수는 결장</a>
    </strong></li>
    <li>
    <a class="nclicks(rig.section)" href="http://sports.news.naver.com/wbaseball/news/read.nhn?oid=109&amp;aid=0003545527">황재균, 트리플A 시즌 4호 홈런 폭발</a>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://sports.news.naver.com/general/news/read.nhn?oid=001&amp;aid=0009296561">냉정하게 손 턴 알파고 뜨거운 눈물 흘린 커제</a>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://sports.news.naver.com/basketball/news/read.nhn?oid=398&amp;aid=0000008585">'새가슴 오명' 스테픈 커리, 올해는 다를까</a>
    </li>
    </ul><ul class="type02 type02_1">
    <li>
    <a class="nclicks(rig.section)" href="http://sports.news.naver.com/wbaseball/news/read.nhn?oid=410&amp;aid=0000391714">우리아스, 마이너  첫 등판에서 6이닝 1실점</a>
    </li>
    <li><strong>
    <a class="nclicks(rig.section)" href="http://sports.news.naver.com/kfootball/news/read.nhn?oid=452&amp;aid=0000000655"> 승부보다 중요한, 이 아름다운 U-20 월드컵</a>
    </strong></li>
    <li>
    <a class="nclicks(rig.section)" href="http://sports.news.naver.com/general/news/read.nhn?oid=481&amp;aid=0000002427">테니스 '꿈을 좇는 아이들' 박소현과 박민종</a>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://sports.news.naver.com/wfootball/news/read.nhn?oid=343&amp;aid=0000071763">왓포드 지휘봉 잡은 실바  "앞으로 기대 된다"</a>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://sports.news.naver.com/golf/news/read.nhn?oid=001&amp;aid=0009297202">김우현, 3년 만 우승 트로피…'제대 후 첫 우승'</a>
    </li>
    </ul><ul class="type02 type02_1">
    <li>
    <a class="nclicks(rig.section)" href="http://sports.news.naver.com/general/news/read.nhn?oid=477&amp;aid=0000075997">구스타프손 vs 테세이라…누가 '자리' 차지할까?</a>
    </li>
    <li><strong>
    <a class="nclicks(rig.section)" href="http://sports.news.naver.com/basketball/news/read.nhn?oid=076&amp;aid=0003098575"> '지지부진' 라틀리프 귀화, 불발 가능성↑</a>
    </strong></li>
    <li>
    <a class="nclicks(rig.section)" href="http://sports.news.naver.com/golf/news/read.nhn?oid=477&amp;aid=0000076022">강수연, 연장 접전 끝 JLPGA 투어 3번째 우승</a>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://sports.news.naver.com/golf/news/read.nhn?oid=470&amp;aid=0000005461">축하를 위해 기다렸다 우승컵 들어올린 김우현</a>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://sports.news.naver.com/esports/news/read.nhn?oid=442&amp;aid=0000058401">'블소앤토너먼트' GC Busan Red 승자조 합류</a>
    </li>
    </ul>
    </div>
    <div class="classfy sd" style="display:none">
    <h5 class="blind">TV연예</h5>
    <ul class="type02">
    <li>
    <a class="nclicks(rig.section)" href="http://entertain.naver.com/read?oid=311&amp;aid=0000738407">김영철 "'따르릉'으로 소원성취, 글로벌하게 터졌으면"</a>
    </li>
    <li><strong>
    <a class="nclicks(rig.section)" href="http://entertain.naver.com/read?oid=001&amp;aid=0009296415">'프듀101' 과열 팬심·편집논란…견제투표에 팬광고까지</a>
    </strong></li>
    <li>
    <a class="nclicks(rig.section)" href="http://entertain.naver.com/read?oid=213&amp;aid=0000967125"> 이파니 "남편 서성민, 운명의 남자…저 정말 열심히 살게요"</a>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://entertain.naver.com/read?oid=109&amp;aid=0003545711">'잠실 입성' 받고 '5연속 대상, 엑소의 '신기록'은 계속 된다  </a>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://entertain.naver.com/read?oid=421&amp;aid=0002755744"> 김희철·이상민·서장훈, 어느새 예능 '중심'에</a>
    </li>
    </ul><ul class="type02 type02_1">
    <li>
    <a class="nclicks(rig.section)" href="http://entertain.naver.com/read?oid=109&amp;aid=0003545497">10주년 FT아일랜드, '사랑앓이' 재탄생(With 김나영)</a>
    </li>
    <li><strong>
    <a class="nclicks(rig.section)" href="http://entertain.naver.com/read?oid=109&amp;aid=0003545549">이정재 "드라마 안하냐고? 출연 제안 많지 않아"</a>
    </strong></li>
    <li>
    <a class="nclicks(rig.section)" href="http://entertain.naver.com/read?oid=076&amp;aid=0003098601">'아는 형님' 오현경, 지금껏 본 적 없는 '新 예능캐릭터' 탄생</a>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://entertain.naver.com/read?oid=025&amp;aid=0002720376">'그후' 권해효 "홍상수 감독에게 고맙고, 짜릿했다" </a>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://entertain.naver.com/read?oid=213&amp;aid=0000967105"> 미란다 커♥에반 스피겔, 부부됐다…LA서 비밀리 웨딩</a>
    </li>
    </ul><ul class="type02 type02_1">
    <li>
    <a class="nclicks(rig.section)" href="http://entertain.naver.com/read?oid=109&amp;aid=0003545408"> 목정남X미스터멍…'무도' 하드캐리한 배정남·크러쉬</a>
    </li>
    <li><strong>
    <a class="nclicks(rig.section)" href="http://entertain.naver.com/read?oid=112&amp;aid=0002924436">'인가' 트와이스, 1위로 5관왕 달성…세븐틴·아이콘 컴백</a>
    </strong></li>
    <li>
    <a class="nclicks(rig.section)" href="http://entertain.naver.com/read?oid=112&amp;aid=0002924386">트와이스·방탄소년단, 5월 아이돌 브랜드평판 1·2위</a>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://entertain.naver.com/read?oid=109&amp;aid=0003545393">'캐리비안5'도 통했다…시리즈 전편 4일만 100만 돌파</a>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://entertain.naver.com/read?oid=311&amp;aid=0000738382"> '당신은' 엄정화·장희진의 기구한 인연, 고부관계될까 </a>
    </li>
    </ul>
    </div>
    <div class="classfy sd" style="display:none">
    <h5 class="blind">경제/생활</h5>
    <ul class="type02">
    <li>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=101&amp;oid=028&amp;aid=0002366263" title=" 강남권에서 발화된 아파트값 급등세… 정부 '규제의 칼' 꺼내들까?  "> 강남권에서 발화된 아파트값 급등세… 정부 '규제의 칼' 꺼내들까?  </a>
    </li>
    <li>
    <strong>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=101&amp;oid=028&amp;aid=0002366260" title=" 일자리 상황판 속 비정규직 비중 '32.8%'가 전부인가요? "> 일자리 상황판 속 비정규직 비중 '32.8%'가 전부인가요? </a>
    </strong>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=101&amp;oid=052&amp;aid=0001015184" title="'일단 사고 보자' 반품 급증 …30·40대 여성이 절반">'일단 사고 보자' 반품 급증 …30·40대 여성이 절반</a>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=101&amp;oid=421&amp;aid=0002755864" title="1조 돌파한 P2P 대출…투자 한도 '연 1천만원'으로 제한">1조 돌파한 P2P 대출…투자 한도 '연 1천만원'으로 제한</a>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=101&amp;oid=015&amp;aid=0003774422" title="2분기도 '깜짝 실적' 코스피 '고공비행' 계속된다">2분기도 '깜짝 실적' 코스피 '고공비행' 계속된다</a>
    </li>
    </ul><ul class="type02 type02_1">
    <li>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=101&amp;oid=001&amp;aid=0009297103" title="보험사에 경품행사 고객정보 팔고, 당첨자 바꿔치기 하고(종합)">보험사에 경품행사 고객정보 팔고, 당첨자 바꿔치기 하고(종합)</a>
    </li>
    <li>
    <strong>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=103&amp;oid=052&amp;aid=0001015170" title=" 내일 기온 더 올라…자외선 주의"> 내일 기온 더 올라…자외선 주의</a>
    </strong>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=103&amp;oid=001&amp;aid=0009296274" title="돌 틈에서 나고 자라 돌 틈으로 돌아가다">돌 틈에서 나고 자라 돌 틈으로 돌아가다</a>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=103&amp;oid=353&amp;aid=0000027070" title="한바탕 놀다가는 세상, 희망가를 불러야쥬">한바탕 놀다가는 세상, 희망가를 불러야쥬</a>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=103&amp;oid=353&amp;aid=0000027028" title='"현대 예술이 어렵다고요? 동시대 담아 더 공감할 수 있죠"'>"현대 예술이 어렵다고요? 동시대 담아 더 공감할 수 있죠"</a>
    </li>
    </ul><ul class="type02 type02_1">
    <li>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=103&amp;oid=001&amp;aid=0009296289" title='"잦은 소화불량·몸살 있으면 담석 의심해봐야"'>"잦은 소화불량·몸살 있으면 담석 의심해봐야"</a>
    </li>
    <li>
    <strong>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=105&amp;oid=001&amp;aid=0009296313" title="'바둑 정복' 알파고, 이제 의료·과학 분야 무한도전 나선다">'바둑 정복' 알파고, 이제 의료·과학 분야 무한도전 나선다</a>
    </strong>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=105&amp;oid=003&amp;aid=0007980222" title="갤럭시S8, 국내 개통량 100만대 돌파…전작 비해 2배 빨라">갤럭시S8, 국내 개통량 100만대 돌파…전작 비해 2배 빨라</a>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=105&amp;oid=001&amp;aid=0009296412" title="감정노동 논란 '사랑합니다, 고객님' 114 인사말 부활하나">감정노동 논란 '사랑합니다, 고객님' 114 인사말 부활하나</a>
    </li>
    <li>
    <a class="nclicks(rig.section)" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=105&amp;oid=421&amp;aid=0002756340" title="세계 최초 지상파 UHD 본방송 'D-3', 결국 '반쪽짜리' 출범">세계 최초 지상파 UHD 본방송 'D-3', 결국 '반쪽짜리' 출범</a>
    </li>
    </ul>
    </div>
    <a class="more_link nclicks(rig.section)" href="http://news.naver.com/main/hotissue/dailyList.nhn">
    <img alt="분야별 주요뉴스 더보기" height="13" src="http://static.news.naver.net/image/news/2009/txt_more_link2.gif" width="33"/>
    </a>
    </div>
    <script type="text/javascript">
    var rightDailyList = {
    id : 'right_dailyList',
    init : function() {
    this.tabList = jindo.$A(jindo.$$('#' + this.id + ' div.category a'));
    this.contentsList = jindo.$A(jindo.$$('#' + this.id + ' div.classfy'));
    var randomIndex = this.getRandomIndex();
    this.toggle(randomIndex);
    jindo.$Fn(this.handler, this).attach(this.tabList, 'mouseover');
    },
    getRandomIndex : function() {
    return Math.floor(Math.random() * this.tabList.length());
    },
    getSelectedIndex : function(el) {
    var selectedIndex;
    this.tabList.forEach(function(value, index, array) {
    if (el.tagName === 'A' && value == el) {
    selectedIndex = index;
    } else if (el.tagName === 'STRONG' && value == el.parentNode) {
    selectedIndex = index;
    }
    });
    return selectedIndex;
    },
    handler : function(event) {
    event.stopDefault();
    var selectedIndex = this.getSelectedIndex(event.element);
    if (this.currentIndex !== selectedIndex) {
    this.toggle(selectedIndex);
    }
    },
    toggle : function(selectedIndex) {
    var self = this;
    this.tabList.forEach(function(value, index, array) {
    var wel = jindo.$Element(value);
    if (selectedIndex === index) {
    wel.html('<strong>' + wel.html() + '</strong>');
    wel.addClass("is_click");
    } else if (self.currentIndex === index) {
    wel.html(wel.first().html());
    wel.removeClass("is_click");
    }
    });
    this.contentsList.forEach(function(value, index, array) {
    var wel = jindo.$Element(value);
    if (selectedIndex === index) {
    wel.show();
    } else {
    wel.hide();
    }
    });
    this.currentIndex = selectedIndex;
    }
    }
    rightDailyList.init();
    </script>
    <div class="section" style="margin-bottom:20px;">
    <h4><a class="nclicks(rig.banner)" href="http://news.naver.com/main/hotissue/sectionList.nhn?mid=hot&amp;sid1=110&amp;cid=1019690">상식in뉴스</a></h4>
    <div class="banner">
    <a class="nclicks(rig.banner)" href="http://news.naver.com/main/hotissue/sectionList.nhn?mid=hot&amp;sid1=110&amp;cid=1019690"><img alt="상식in뉴스" src="http://imgnews.naver.net/image/upload/item/2015/03/18/095339765_%25BF%25EC%25C3%25F8%25B9%25E8%25B3%25CA_244X90.png" width="244"/></a>
    </div>
    <a class="more_link nclicks(rig.banner)" href="http://news.naver.com/main/hotissue/sectionList.nhn?mid=hot&amp;sid1=110&amp;cid=1019690"><img alt="상식in뉴스 더보기" height="13" src="http://static.news.naver.net/image/news/2009/txt_more_link2.gif" width="33"/></a>
    </div>
    <div class="section hottopic">
    <h4>뉴스토픽</h4>
    <p>
    <span class="newstopic_error_text">일시적 오류로 최신 정보를 제공할 수 없습니다.</span>
    <span class="newstopic_time">2017.05.28. 11:30 ~ 14:30 기준<a class="newstopic_help nclicks(rig.hotopic)" href="#" onclick="news.external.OPS.helpHotTopicKeyword(); return false;" target="_blank" title="새창"><span class="blind">도움말</span></a></span>
    </p>
    <ol class="type15">
    <li><a class="nclicks(rig.hotopic)" href="http://search.naver.com/search.naver?where=nexearch&amp;query=%EC%9D%B8%EA%B8%B0%EA%B0%80%EC%9A%94%20%ED%8A%B8%EC%99%80%EC%9D%B4%EC%8A%A4&amp;ie=utf8&amp;sm=nws_htk" target="_blank" title="인기가요 트와이스"><span class="rank"><em>1</em></span> <strong class="title">인기가요 트와이스</strong> <span class="rank2"><img alt="new" height="5" src="http://static.news.naver.net/image/news/2010/ico_right_new.gif" width="21"/></span></a></li>
    <li><a class="nclicks(rig.hotopic)" href="http://search.naver.com/search.naver?where=nexearch&amp;query=%EA%B3%B5%EB%AC%B4%EC%83%81%20%EC%A7%88%EB%B3%91&amp;ie=utf8&amp;sm=nws_htk" target="_blank" title="공무상 질병"><span class="rank"><em>2</em></span> <strong class="title">공무상 질병</strong> <span class="rank2"><img alt="new" height="5" src="http://static.news.naver.net/image/news/2010/ico_right_new.gif" width="21"/></span></a></li>
    <li><a class="nclicks(rig.hotopic)" href="http://search.naver.com/search.naver?where=nexearch&amp;query=FT%EC%95%84%EC%9D%BC%EB%9E%9C%EB%93%9C%20%EC%82%AC%EB%9E%91%EC%95%93%EC%9D%B4&amp;ie=utf8&amp;sm=nws_htk" target="_blank" title="FT아일랜드 사랑앓이"><span class="rank"><em>3</em></span> <strong class="title">FT아일랜드 사랑앓이</strong> <span class="rank2"><img alt="new" height="5" src="http://static.news.naver.net/image/news/2010/ico_right_new.gif" width="21"/></span></a></li>
    <li><a class="nclicks(rig.hotopic)" href="http://search.naver.com/search.naver?where=nexearch&amp;query=%EC%9A%B0%EC%9B%90%EC%8B%9D%20%EC%9B%90%EB%82%B4%EB%8C%80%ED%91%9C&amp;ie=utf8&amp;sm=nws_htk" target="_blank" title="우원식 원내대표"><span class="rank"><em>4</em></span> <strong class="title">우원식 원내대표</strong> <span class="rank2"></span></a></li>
    <li><a class="nclicks(rig.hotopic)" href="http://search.naver.com/search.naver?where=nexearch&amp;query=%EA%B5%AC%EC%9D%98%EC%97%AD%20%EC%B0%B8%EC%82%AC%201%EC%A3%BC%EA%B8%B0&amp;ie=utf8&amp;sm=nws_htk" target="_blank" title="구의역 참사 1주기"><span class="rank"><em>5</em></span> <strong class="title">구의역 참사 1주기</strong> <span class="rank2"></span></a></li>
    <li><a class="nclicks(rig.hotopic)" href="http://search.naver.com/search.naver?where=nexearch&amp;query=%EC%8C%88%20%EB%A7%88%EC%9D%B4%EC%9B%A8%EC%9D%B4%20%EA%B9%80%EC%A7%80%EC%9B%90&amp;ie=utf8&amp;sm=nws_htk" target="_blank" title="쌈 마이웨이 김지원"><span class="rank"><em>6</em></span> <strong class="title">쌈 마이웨이 김지원</strong> <span class="rank2"><img alt="new" height="5" src="http://static.news.naver.net/image/news/2010/ico_right_new.gif" width="21"/></span></a></li>
    <li><a class="nclicks(rig.hotopic)" href="http://search.naver.com/search.naver?where=nexearch&amp;query=%EC%98%A4%EC%8A%B9%ED%99%98%201%EC%9D%B4%EB%8B%9D%202K%20%EB%AC%B4%EC%8B%A4%EC%A0%90&amp;ie=utf8&amp;sm=nws_htk" target="_blank" title="오승환 1이닝 2K 무실점"><span class="rank"><em>7</em></span> <strong class="title">오승환 1이닝 2K 무실점</strong> <span class="rank2"><img alt="new" height="5" src="http://static.news.naver.net/image/news/2010/ico_right_new.gif" width="21"/></span></a></li>
    <li><a class="nclicks(rig.hotopic)" href="http://search.naver.com/search.naver?where=nexearch&amp;query=%EB%AC%B8%EC%9E%AC%EC%9D%B8%20%EC%A0%95%EB%B6%80&amp;ie=utf8&amp;sm=nws_htk" target="_blank" title="문재인 정부"><span class="rank"><em>8</em></span> <strong class="title">문재인 정부</strong> <span class="rank2"></span></a></li>
    <li><a class="nclicks(rig.hotopic)" href="http://search.naver.com/search.naver?where=nexearch&amp;query=%EB%AF%B8%EC%9A%B0%EC%83%88%20%EB%B0%95%EC%88%98%ED%99%8D&amp;ie=utf8&amp;sm=nws_htk" target="_blank" title="미우새 박수홍"><span class="rank"><em>9</em></span> <strong class="title">미우새 박수홍</strong> <span class="rank2"><img alt="new" height="5" src="http://static.news.naver.net/image/news/2010/ico_right_new.gif" width="21"/></span></a></li>
    <li><a class="nclicks(rig.hotopic)" href="http://search.naver.com/search.naver?where=nexearch&amp;query=SNL9%20%ED%98%84%EC%9A%B0&amp;ie=utf8&amp;sm=nws_htk" target="_blank" title="SNL9 현우"><span class="rank"><em>10</em></span> <strong class="title">SNL9 현우</strong> <span class="rank2"></span></a></li>
    </ol>
    </div>
    </div>
    </td>
    </tr>
    </table>
    <hr/>
    <div class="index">
    <div class="page_navi">
    <a class="prev" href="javascript:history.back();">이전페이지로 돌아가기</a>
    <span class="bar">|</span><a class="top" href="#" onclick="window.scrollTo(0,0);oWebAccessibilityUtil.moveFocus(document.getElementById('da_base'));return false;">맨위로</a>
    </div>
    <div class="index_tab" id="index.press_category">
    <a class="spr stit nclicks(fot.press)" href="#" id="index.press.btn">언론사 목록<span class="loading" style="display:none;">로딩 중</span></a>
    <div id="index.press.area" style="display:none;"></div>
    <a class="spr stit2 nclicks(fot.section)" href="#" id="index.category.btn">분야별 목록<span class="loading" style="display:none;">로딩 중</span></a>
    <div class="index_content02" id="index.category.area" style="display:none;"></div>
    <span class="stit3">
    <span class="bar">|</span>
    <a class="spr nclicks(fot.scrap)" href="/main/scrap/index.nhn">마이스크랩</a>
    </span>
    </div>
    <dl class="type01 fs11">
    <dt></dt>
    <dd>
    </dd>
    </dl>
    </div>
    <hr/>
    <div id="footer">
    <ul>
    <li class="first"><a class="nclicks(fot.agreement)" href="http://www.naver.com/rules/service.html">이용약관</a></li>
    <li><a href="/main/ombudsman/index.nhn">서비스 안내</a></li>
    <li><a class="nclicks(fot.editor)" href="/main/ombudsman/edit.nhn?mid=omb">기사배열 원칙 책임자 : 유봉석</a></li>
    <li>청소년 보호 책임자 : 정연아</li>
    <li><strong><a class="nclicks(fot.privacy)" href="http://www.naver.com/rules/privacy.html">개인정보처리방침</a></strong></li>
    <li><a class="nclicks(fot.disclaimer)" href="http://www.naver.com/rules/disclaimer.html">책임의 한계와 법적고지</a></li>
    <li><a class="nclicks(fot.help)" href="#" onclick="news.external.OPS.helpNews(); return false;">뉴스 고객센터</a></li>
    </ul>
    <p class="copyright">본 콘텐츠의 저작권은 제공처 또는 네이버에 있으며 이를 무단 이용하는 경우 저작권법 등에 따라 법적책임을 질 수 있습니다.</p>
    <address class="address_nhn">
    <a class="logo nclicks(fot.naver)" href="http://www.navercorp.com/" target="_blank"><span class="blind">NAVER</span></a>
    <em>Copyright ©</em>
    <a class="nclicks(fot.navercorp)" href="http://www.navercorp.com/" target="_blank">NAVER Corp.</a>
    <span>All Rights Reserved.</span>
    </address>
    <!-- -->
    </div>
    <script type="text/javascript">
    
    
    
    new news.lnb.NewsSearchController('lnb.searchForm');
    
    
    
    var lnb_weatherRolling = new news.lnb.LineRolling('lnb.weather',{size:26,moveSize:3,moveInterval:30});
    var lnb_mainnewsRolling = new news.lnb.LineRolling('lnb.mainnews',{size:17,moveSize:2,moveInterval:30,isRandomStart:true});
    var lnb_Rolling = new news.lnb.SynchronizedRolling({interval:3000});
    lnb_Rolling.push(lnb_weatherRolling);
    lnb_Rolling.push(lnb_mainnewsRolling);
    lnb_Rolling.start();
    
    
    var index_tab = new news.index.TabController('index.press_category');
    index_tab.add(new news.index.Tab('index.press.btn','stit','index.press.area','/main/ajax/bottomIndex/press.nhn'));
    index_tab.add(new news.index.Tab('index.category.btn','stit2','index.category.area','/main/ajax/bottomIndex/category.nhn'));
    jindo.$Fn(index_tab.toggle, index_tab).attach(index_tab.tabs.keys(), 'click');
    
    
    (function() {
    	news.right.RightSideFloatingManager.init();
    })();
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    var helpMessageLM = new jindo.LayerManager('messageLayer',{sCheckEvent:"click",nCheckDelay:0,nShowDelay:0,nHideDelay:0}).link('helpMessage');
    var helpMessageButton = new jindo.$('helpMessage');
    jindo.$Fn(helpMessageLM.toggle, helpMessageLM).attach(helpMessageButton,'click');
    
    
    
    
    
    
    
    
    
    
    
    
    	
    	lcs_do();
    
    
    
    
    
    
    news.startup();
    
    
    jindo.$Fn(function(){
        var welSkip = jindo.$Element("u_skip");
        if (welSkip === null) {
            return;
        }
        welSkip.delegate("click", "a", function(weEvent){
            weEvent.stopDefault();
            var elTarget = jindo.$(weEvent.element.href.split("#")[1]);
            oWebAccessibilityUtil.moveFocus(elTarget);
        });
    }).attach(window, "load");
    
    
    document.documentElement.setAttribute('data-useragent',navigator.userAgent);
    </script>
    </div>
    </body>
    </html>
    

* **div의 info class에 해당하는 내용 가져오기**


```python
item = soup.find_all('div', 'info')
print(item)
```

    [<div class="info">
    <span class="pht"></span>
    <span class="press">서울신문</span> <span class="bar"></span> <span class="time">2시간전</span> <span class="bar"></span> <a class="go_naver" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=106&amp;oid=081&amp;aid=0002824625" onclick="" target="_blank">네이버뉴스</a>
    <!-- 개인화플러그인 -->
    <div class="head_social share_area">
    <span class="bar"></span>
    <a class="btn_share btn_social naver-splugin nclicks(STA.share)" data-mail-display="off" data-me-display="off" data-oninitialize="splugin_oninitialize('this');" data-option="{baseElement:'spiButton0', layerPosition:'outside-bottom', align:'right', top:4, left:0, marginLeft:8, marginTop:10}" data-service-name="뉴스" data-source-name="서울신문" data-style="unity-v2" data-title="‘아는형님’ 오현경, “강호동이 이상형..고백했으면 사귀었을 것”" data-url="http://en.seoul.co.kr/news/newsView.php?id=20170528500034&amp;wlog_tag3=naver" href="#" id="spiButton0" title="소셜공유하기">
    <span class="blind naver-splugin-c">소셜공유하기</span>
    </a>
    </div>
    <!-- 개인화플러그인 -->
    </div>, <div class="info">
    <span class="pht"></span>
    <span class="press">동아일보</span> <span class="bar"></span> <span class="time">5시간전</span> <span class="bar"></span> <a class="go_naver" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=106&amp;oid=020&amp;aid=0003068035" onclick="" target="_blank">네이버뉴스</a>
    <!-- 개인화플러그인 -->
    <div class="head_social share_area">
    <span class="bar"></span>
    <a class="btn_share btn_social naver-splugin nclicks(STA.share)" data-mail-display="off" data-me-display="off" data-oninitialize="splugin_oninitialize('this');" data-option="{baseElement:'spiButton1', layerPosition:'outside-bottom', align:'right', top:4, left:0, marginLeft:8, marginTop:10}" data-service-name="뉴스" data-source-name="동아일보" data-style="unity-v2" data-title="‘아는 형님’ 오현경, 별명 ‘엉뚱이’…“힙 사이즈 36, 엉덩이 뚱뚱해서 창피”" data-url="http://news.donga.com/3/all/20170528/84596732/2" href="#" id="spiButton1" title="소셜공유하기">
    <span class="blind naver-splugin-c">소셜공유하기</span>
    </a>
    </div>
    <!-- 개인화플러그인 -->
    </div>, <div class="info">
    <span class="pht"></span>
    <span class="press">조선일보</span> <span class="bar"></span> <span class="time">16시간전</span> <span class="bar"></span> <a class="go_naver" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=004&amp;oid=023&amp;aid=0003283677" onclick="" target="_blank">네이버뉴스</a>
    <!-- 개인화플러그인 -->
    <div class="head_social share_area">
    <span class="bar"></span>
    <a class="btn_share btn_social naver-splugin nclicks(STA.share)" data-mail-display="off" data-me-display="off" data-oninitialize="splugin_oninitialize('this');" data-option="{baseElement:'spiButton2', layerPosition:'outside-bottom', align:'right', top:4, left:0, marginLeft:8, marginTop:10}" data-service-name="뉴스" data-source-name="조선일보" data-style="unity-v2" data-title="'아는형님' 딘딘, 마동석과 친분샷…&quot;마블리, 마요미&quot;" data-url="http://news.chosun.com/site/data/html_dir/2017/05/27/2017052701457.html" href="#" id="spiButton2" title="소셜공유하기">
    <span class="blind naver-splugin-c">소셜공유하기</span>
    </a>
    </div>
    <!-- 개인화플러그인 -->
    </div>, <div class="info">
    <span class="pht"></span>
    <span class="press">세계일보</span> <span class="bar"></span> <span class="time">19시간전</span> <span class="bar"></span> <a class="go_naver" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=106&amp;oid=022&amp;aid=0003177038" onclick="" target="_blank">네이버뉴스</a>
    <!-- 개인화플러그인 -->
    <div class="head_social share_area">
    <span class="bar"></span>
    <a class="btn_share btn_social naver-splugin nclicks(STA.share)" data-mail-display="off" data-me-display="off" data-oninitialize="splugin_oninitialize('this');" data-option="{baseElement:'spiButton3', layerPosition:'outside-bottom', align:'right', top:4, left:0, marginLeft:8, marginTop:10}" data-service-name="뉴스" data-source-name="세계일보" data-style="unity-v2" data-title="'아는형님' 딘딘, 마동석과 부자지간 뺨치는 친분샷 '훈훈'" data-url="http://www.segye.com/content/html/2017/05/27/20170527001011.html?OutUrl=naver" href="#" id="spiButton3" title="소셜공유하기">
    <span class="blind naver-splugin-c">소셜공유하기</span>
    </a>
    </div>
    <!-- 개인화플러그인 -->
    </div>, <div class="info">
    <span class="pht"></span>
    <span class="press">서울신문</span> <span class="bar"></span> <span class="time">19시간전</span> <span class="bar"></span> <a class="go_naver" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=106&amp;oid=081&amp;aid=0002824583" onclick="" target="_blank">네이버뉴스</a>
    <!-- 개인화플러그인 -->
    <div class="head_social share_area">
    <span class="bar"></span>
    <a class="btn_share btn_social naver-splugin nclicks(STA.share)" data-mail-display="off" data-me-display="off" data-oninitialize="splugin_oninitialize('this');" data-option="{baseElement:'spiButton4', layerPosition:'outside-bottom', align:'right', top:4, left:0, marginLeft:8, marginTop:10}" data-service-name="뉴스" data-source-name="서울신문" data-style="unity-v2" data-title="‘아는 형님’ 오현경, 48세 맞아? 놀라운 동안 외모 “나이 들어도 예쁜… 절세미인”" data-url="http://en.seoul.co.kr/news/newsView.php?id=20170527500058&amp;wlog_tag3=naver" href="#" id="spiButton4" title="소셜공유하기">
    <span class="blind naver-splugin-c">소셜공유하기</span>
    </a>
    </div>
    <!-- 개인화플러그인 -->
    </div>, <div class="info">
    <span class="pht"></span>
    <span class="press">조선일보</span> <span class="bar"></span> <span class="time">16시간전</span> <span class="bar"></span> <a class="go_naver" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=004&amp;oid=023&amp;aid=0003283679" onclick="" target="_blank">네이버뉴스</a>
    <!-- 개인화플러그인 -->
    <div class="head_social share_area">
    <span class="bar"></span>
    <a class="btn_share btn_social naver-splugin nclicks(STA.share)" data-mail-display="off" data-me-display="off" data-oninitialize="splugin_oninitialize('this');" data-option="{baseElement:'spiButton5', layerPosition:'outside-bottom', align:'right', top:4, left:0, marginLeft:8, marginTop:10}" data-service-name="뉴스" data-source-name="조선일보" data-style="unity-v2" data-title="'아는형님' 오현경, 정시아와 동안 대결…&quot;끝판왕 누구&quot;" data-url="http://news.chosun.com/site/data/html_dir/2017/05/27/2017052701462.html" href="#" id="spiButton5" title="소셜공유하기">
    <span class="blind naver-splugin-c">소셜공유하기</span>
    </a>
    </div>
    <!-- 개인화플러그인 -->
    </div>, <div class="info">
    <span class="pht"></span>
    <span class="press">세계일보</span> <span class="bar"></span> <span class="time">17시간전</span> <span class="bar"></span> <a class="go_naver" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=106&amp;oid=022&amp;aid=0003177041" onclick="" target="_blank">네이버뉴스</a>
    <!-- 개인화플러그인 -->
    <div class="head_social share_area">
    <span class="bar"></span>
    <a class="btn_share btn_social naver-splugin nclicks(STA.share)" data-mail-display="off" data-me-display="off" data-oninitialize="splugin_oninitialize('this');" data-option="{baseElement:'spiButton6', layerPosition:'outside-bottom', align:'right', top:4, left:0, marginLeft:8, marginTop:10}" data-service-name="뉴스" data-source-name="세계일보" data-style="unity-v2" data-title="'아는형님' 오현경, 강호동과 28년 우정...첫 만남 장소가 '엘레베이터'" data-url="http://www.segye.com/content/html/2017/05/27/20170527001053.html?OutUrl=naver" href="#" id="spiButton6" title="소셜공유하기">
    <span class="blind naver-splugin-c">소셜공유하기</span>
    </a>
    </div>
    <!-- 개인화플러그인 -->
    </div>, <div class="info">
    <span class="pht"></span>
    <span class="press">한국일보</span> <span class="bar"></span> <span class="time">6일전</span> <span class="bar"></span> <a class="go_naver" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=106&amp;oid=469&amp;aid=0000204286" onclick="" target="_blank">네이버뉴스</a>
    <!-- 개인화플러그인 -->
    <div class="head_social share_area">
    <span class="bar"></span>
    <a class="btn_share btn_social naver-splugin nclicks(STA.share)" data-mail-display="off" data-me-display="off" data-oninitialize="splugin_oninitialize('this');" data-option="{baseElement:'spiButton7', layerPosition:'outside-bottom', align:'right', top:4, left:0, marginLeft:8, marginTop:10}" data-service-name="뉴스" data-source-name="한국일보" data-style="unity-v2" data-title="‘아는 형님’ 속 아재들이 야속한 까닭" data-url="http://www.hankookilbo.com/v/843e9372f6024d558bb5d26fc6e55ad6" href="#" id="spiButton7" title="소셜공유하기">
    <span class="blind naver-splugin-c">소셜공유하기</span>
    </a>
    </div>
    <!-- 개인화플러그인 -->
    </div>, <div class="info">
    <span class="pht"></span>
    <span class="press">서울신문</span> <span class="bar"></span> <span class="time">7일전</span> <span class="bar"></span> <a class="go_naver" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=106&amp;oid=081&amp;aid=0002822674" onclick="" target="_blank">네이버뉴스</a>
    <!-- 개인화플러그인 -->
    <div class="head_social share_area">
    <span class="bar"></span>
    <a class="btn_share btn_social naver-splugin nclicks(STA.share)" data-mail-display="off" data-me-display="off" data-oninitialize="splugin_oninitialize('this');" data-option="{baseElement:'spiButton8', layerPosition:'outside-bottom', align:'right', top:4, left:0, marginLeft:8, marginTop:10}" data-service-name="뉴스" data-source-name="서울신문" data-style="unity-v2" data-title="‘아는 형님’ 트와이스 쯔위, 김영철과 짝꿍 회상 “내가 불쌍해 보였다”" data-url="http://en.seoul.co.kr/news/newsView.php?id=20170520500055&amp;wlog_tag3=naver" href="#" id="spiButton8" title="소셜공유하기">
    <span class="blind naver-splugin-c">소셜공유하기</span>
    </a>
    </div>
    <!-- 개인화플러그인 -->
    </div>, <div class="info">
    <span class="pht"></span>
    <span class="press">조선일보</span> <span class="bar"></span> <span class="time">7일전</span> <span class="bar"></span> <a class="go_naver" href="http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=004&amp;oid=023&amp;aid=0003281769" onclick="" target="_blank">네이버뉴스</a>
    <!-- 개인화플러그인 -->
    <div class="head_social share_area">
    <span class="bar"></span>
    <a class="btn_share btn_social naver-splugin nclicks(STA.share)" data-mail-display="off" data-me-display="off" data-oninitialize="splugin_oninitialize('this');" data-option="{baseElement:'spiButton9', layerPosition:'outside-bottom', align:'right', top:4, left:0, marginLeft:8, marginTop:10}" data-service-name="뉴스" data-source-name="조선일보" data-style="unity-v2" data-title="오현경, '아는 형님'도 숨막힌 뒤태" data-url="http://news.chosun.com/site/data/html_dir/2017/05/20/2017052001809.html" href="#" id="spiButton9" title="소셜공유하기">
    <span class="blind naver-splugin-c">소셜공유하기</span>
    </a>
    </div>
    <!-- 개인화플러그인 -->
    </div>]
    

* **div class="info"에서 a태그에 달려 있는 각 네이버뉴스의 url 주소 가져오기**


```python
article = item[0].find('a')['href']
print(article)
```

    http://news.naver.com/main/read.nhn?mode=LSD&mid=shm&sid1=106&oid=081&aid=0002824625
    


```python
news_url = urllib.request.urlopen(article)
print(news_url)
```

    <http.client.HTTPResponse object at 0x000002210B1D89E8>
    

* **네이버뉴스에서 추출한 url에서 내용을 BeautifulSoup으로 가져오기**


```python
soup2 = BeautifulSoup(news_url, "lxml", from_encoding = "utf-8")
print(soup2)
```

    <!DOCTYPE html>
    <html lang="ko">
    <head>
    <title id="browse_title">‘아는형님’ 오현경, “강호동이 이상형..고백했으면 사귀었을 것” :: 네이버 TV연예</title>
    <meta charset="utf-8"/>
    <meta content="IE=edge" http-equiv="X-UA-Compatible"/>
    <script charset="utf-8" type="text/javascript">
    var doc = document.documentElement;
    doc.setAttribute('data-useragent', navigator.userAgent);
    </script>
    <link href="http://static.news.naver.net/image/news/2014/favicon/favicon.ico" rel="shortcut icon" type="image/x-icon"/>
    <script src="http://static.entertain.naver.net/resources/20170522_165620/js/infra/jindo/jindo.desktop.ns.min.js" type="text/javascript"></script>
    <script src="http://static.entertain.naver.net/resources/20170522_165620/js/infra/common/enter.define.js" type="text/javascript"></script>
    <script src="http://static.entertain.naver.net/resources/20170522_165620/js/infra/image/imageError.js" type="text/javascript"></script>
    <script type="text/javascript">
    document.domain = 'naver.com';
    </script>
    <script type="text/javascript">
    (function(){
    	if (jindo.$Agent().navigator().mobile && (jindo.$Cookie().get("enter_pc") != "true")) {
    		if((window.location.href.indexOf("/photo/issue/") > 0) && (window.location.hash.indexOf("cid") > 0)) {
    		    var hashValue = window.location.hash;
    		    var obj = jindo.$S(hashValue).parseString()
    			if(obj['#cid'] != '' && obj['iid']) {
    				window.location.href = "http://m.entertain.naver.com/entertain?id=" + obj['#cid'] + "&imgId=" + obj['iid'] + "&listUrl=http://m.entertain.naver.com";
    				return;
    			}
    		} 
    		window.location.href = "http://m.entertain.naver.com/read?oid=081&aid=0002824625";
    	}	
    })();
    </script>
    <link href="http://static.news.naver.net/image/news/m/2014/press_logo/enter_favicon/2016/iOS/iOS8_180X180_iphone6plus.png" rel="apple-touch-icon-precomposed"/>
    <script>
    function imageErrorDetector(imgCount, imgUrl){
    	if (jindo.$Element("nav_btn_img"+imgCount)) {
    		jindo.$Element("nav_btn_img"+imgCount).hide();
    	}
    	(new Image()).src = '/imgError?url='+escape(location.href)+'&imgUrl='+escape(imgUrl);
    }
    </script>
    <link href="http://static.entertain.naver.net/resources/20170522_165620/css/generated/enter.css" rel="stylesheet" type="text/css"/>
    <meta content="‘아는형님’ 오현경, “강호동이 이상형..고백했으면 사귀었을 것”" property="og:title"/>
    <meta content="article" property="og:type"/>
    <meta content="http://entertain.naver.com/read?oid=081&amp;aid=0002824625" property="og:url"/>
    <meta content="http://mimgnews2.naver.net/image/origin/081/2017/05/28/2824625.jpg" property="og:image"/>
    <meta content="[서울신문 En] 배우 오현경이 “강호동이 이상형이다”고 고백했다. 오현경은 27일 JTBC ‘아는 형님’에 출연해 이같이 말했다. 강호동과 25년 지기 친구인 오현경은 “나보고 반한 적 없어?”라고 ‘기습’ 질문했" property="og:description"/>
    <meta content="서울신문 | 네이버 TV연예" property="og:article:author"/>
    <meta content="summary_large_image" name="twitter:card"/>
    <meta content="‘아는형님’ 오현경, “강호동이 이상형..고백했으면 사귀었을 것”" name="twitter:title"/>
    <meta content="네이버 TV연예" name="twitter:site"/>
    <meta content="서울신문" name="twitter:creator"/>
    <meta content="http://mimgnews2.naver.net/image/origin/081/2017/05/28/2824625.jpg" name="twitter:image"/>
    <meta content="[서울신문 En] 배우 오현경이 “강호동이 이상형이다”고 고백했다. 오현경은 27일 JTBC ‘아는 형님’에 출연해 이같이 말했다. 강호동과 25년 지기 친구인 오현경은 “나보고 반한 적 없어?”라고 ‘기습’ 질문했" name="twitter:description"/>
    </head>
    <body class="end" id="ent_body">
    <script src="http://static.entertain.naver.net/resources/20170522_165620/js/generated/naver.enter.article.read.font.util.js" type="text/javascript"></script>
    <div id="wrap">
    <div id="u_skip">
    <a href="#lnb"><span class="blind">메인 메뉴로 바로가기</span></a>
    <a href="#content"><span class="blind">본문으로 바로가기</span></a>
    </div>
    <div id="header">
    <div class="header_area">
    <div class="gnb_wrap">
    <!-- GNB 마크업 -->
    <div class="" id="gnb"></div>
    <!-- //GNB 마크업 -->
    <input alt="검색" class="btn_search" id="btn_search1" type="submit"/>
    <label class="blind" for="btn_search1">검색</label>
    <div class="search_area" id="search_area">
    <div class="box"><form action="http://news.search.naver.com/search.naver" onsubmit="nclk(this, 'lnb.search', '', '');" target="_blank">
    <input name="where" type="hidden" value="news"/>
    <input name="sm" type="hidden" value="ent_nex"/>
    <input name="ie" type="hidden" value="utf8"/>
    <input class="search_txt" id="search_text" name="query" type="text"/>
    <input alt="검색" class="btn_search" id="btn_search2" type="submit"/>
    <label class="blind" for="btn_search2">검색</label>
    </form>
    </div>
    <a class="btn_close" href="#" id="btn_search_close" title="닫기"><span class="icon"></span><span class="blind">검색창 닫기</span></a>
    </div>
    </div>
    <div class="snb_wrap">
    <h1>
    <a class="logo_naver" href="http://www.naver.com" onclick="nclk(this, 'STA.naver', '', '');"><span class="blind">NAVER</span></a>
    <a class="logo_enter" href="/home" onclick="nclk(this, 'STA.entertain', '', '');"><span class="blind">TV연예</span></a>
    <span class="bar"></span>
    <a class="logo_news" href="http://news.naver.com" onclick="nclk(this, 'STA.news', '', '');"><span class="blind">뉴스</span></a>
    </h1>
    </div>
    <div class="lnb_wrap" id="lnb">
    <ul class="lnb_lst">
    <li class="on"><a class="lnb_home" href="/home" onclick="nclk(this, 'lnb.home', '', '');"><em><span class="blind">TV연예홈</span></em></a></li>
    <li><a class="lnb_tv" href="/tv" onclick="nclk(this, 'lnb.tv', '', '');"><em><span class="blind">TV</span></em></a></li>
    <li><a class="lnb_photo" href="/photo" onclick="nclk(this, 'lnb.pho', '', '');"><em><span class="blind">포토</span></em></a></li>
    <li><a class="lnb_ranking" href="/ranking" onclick="nclk(this, 'lnb.rank', '', '');"><em><span class="blind">랭킹</span></em></a></li>
    <li><a class="lnb_movie" href="/movie" onclick="nclk(this, 'lnb.movie', '', '');"><em><span class="blind">영화</span></em></a></li>
    <li><a class="lnb_music" href="/music" onclick="nclk(this, 'lnb.music', '', '');"><em><span class="blind">뮤직</span></em></a></li>
    <li><a class="lnb_now" href="/now" onclick="nclk(this, 'lnb.now', '', '');"><em><span class="blind">NOW</span></em></a></li>
    <li><a class="lnb_star" href="/starcast" onclick="nclk(this, 'lnb.star', '', '');"><em><span class="blind">스타캐스트</span></em></a></li>
    <li><a class="lnb_vlive" href="http://www.vlive.tv" onclick="nclk(this, 'lnb.vlive', '', '');" target="_blank"><em><span class="blind">Vlive</span></em></a></li>
    </ul>
    </div>
    </div>
    <span class="bg_header"></span>
    </div>
    <div id="content">
    <div class="end_ct">
    <!-- 상단 유틸 -->
    <div class="end_top_util">
    <!-- 폰트 설정 -->
    <div class="font_setup">
    <div class="f_family_set">
    <a class="btn_fselect" href="#" id="btnFontFamily" onclick="nclk(this, 'art.font', '', '');"><span class="sp_ico"></span>글꼴</a>
    <ul class="f_family_lst" id="fontFamilyList">
    <li><a class="f_nn" href="javascript:enter.read.util.fontFamily.changeFont('font1');">나눔고딕</a></li>
    <li><a class="f_mg" href="javascript:enter.read.util.fontFamily.changeFont('font2');">맑은고딕</a></li>
    <li><a class="f_du" href="javascript:enter.read.util.fontFamily.changeFont('font3');">돋움체</a></li>
    <li><a class="f_bt" href="javascript:enter.read.util.fontFamily.changeFont('font4');">바탕체</a></li>
    </ul>
    </div>
    <a class="btn_fsize fs_sm" href="#" id="btnFontSizeSub" onclick="nclk(this, 'art.sizedw', '', '');"><span class="sp_ico"><span class="blind">본문 사이즈 작게</span></span></a>
    <a class="btn_fsize fs_big" href="#" id="btnFontSizeAdd" onclick="nclk(this, 'art.sizeup', '', '');"><span class="sp_ico"><span class="blind">본문 사이즈 크게</span></span></a>
    </div>
    <!-- //폰트 설정 -->
    <a class="btn_print" data-href="/print?oid=081&amp;aid=0002824625" href="#" id="btnPrint" onclick="nclk(this, 'art.print', '', '');"><span class="sp_ico"><span class="blind">인쇄하기</span></span></a>
    </div>
    <!-- //상단 유틸 -->
    <!-- 기사 본문 -->
    <div class="end_ct_area">
    <div class="press_logo">
    <a alt="언론사 이동" class="press_logo" href="http://www.seoul.co.kr/" onclick="nclk(this, 'art.logo', '', '');" target="_blank">
    <img alt="서울신문" height="40" src="http://imgnews.naver.net/image/upload/office_logo/pc/2015/09/20/top_081_20150920185020.jpg?20170522_165620" width="85"/>
    </a>
    </div>
    <!-- 기사 상단 -->
    <p class="end_tit">‘아는형님’ 오현경, “강호동이 이상형..고백했으면 사귀었을 것”</p>
    <div class="article_info">
    <span class="author"><span class="bar"></span>기사입력<em>2017.05.28 오후 2:31</em></span>
    <a alt="기사원문" class="btn_news_origin" href="http://en.seoul.co.kr/news/newsView.php?id=20170528500034&amp;wlog_tag3=naver" onclick="nclk(this, 'art.art', '', '');" target="_blank">기사원문</a>
    <a class="reply_count" href="/comment/list?oid=081&amp;aid=0002824625" id="cmt_count" onclick="nclk(this, 'art.com', '', '');"><span class="bar"></span><span class="sp_ico"><span class="blind">댓글</span></span></a>
    </div>
    <!-- //기사 상단 -->
    <!-- 기사 본문 -->
    <div class="end_body_wrp">
    <!-- 본문 내용 - 언론사별 편집된 기사 내용 -->
    <div class="article_body font1 size3" id="articeBody">
    <!-- [D] 본문 폰트 설정 class 안내
    							나눔고딕 : font1
    							맑은고딕 : font2
    							돋움 : font3
    							바탕 : font4
    							폰트 사이즈 1 : size1
    							폰트 사이즈 2 : size2
    							폰트 사이즈 3 : size3
    							폰트 사이즈 4 : size4
    							폰트 사이즈 5 : size5
    						-->
    						
    							
    						
    						[서울신문 En]<br/><span class="end_photo_org"> <img id="img1" onerror='javascript:this.src="http://static.news.naver.net/image/news/2009/press/empty.png";javascript:this.height="0px";javascript:imageErrorDetector(1, "http://mimgnews1.naver.net/image/081/2017/05/28/0002824625_001_20170528143116159.jpg");' src="http://mimgnews1.naver.net/image/081/2017/05/28/0002824625_001_20170528143116159.jpg?type=w540"/>
    <em class="img_desc">‘아는형님’ 오현경</em></span>배우 오현경이 “강호동이 이상형이다”고 고백했다.<!-- MobileAdNew center --><br/><br/>오현경은 27일 JTBC ‘아는 형님’에 출연해 이같이 말했다. 강호동과 25년 지기 친구인 오현경은 “나보고 반한 적 없어?”라고 ‘기습’ 질문했다.<br/><br/>이에 강호동은 고개를 숙이고 손수건으로 이마 땀을 닦는 등 부끄러움을 감추지 못했다. 이어 오현경은 “만약 호동이가 대시했다면 사귀었을 것”이라고 말했다.<br/><br/>또한 오현경은 “사실 내 이상형은 강호동이다”고 말해 출연진을 당황하게 만들었다. 이어 그는 “지금 장난치는 거야. 너 결혼했는데 그러면 안된다”고 덧붙여 웃음을 자아냈다.<br/><br/>한편 이날 강호동은 “오현경과 친구로 지낸지는 25년, 실제로 본 지는 28년 된다”며 오현경과의 우정을 과시했다.<br/><br/>그는 “오현경이 (1989년) 미스코리아가 됐을 때 나도 백두장사가 됐다. 모 언론 인터뷰를 갔는데 그 때 엘리베이터에서 만났다”고 회상했다.<br/><br/>사진 = 서울신문DB<!-- MobileAdNew center --><br/><br/>연예팀 seoulen@seoul.co.kr<br/><br/>▶ [<a href="http://en.seoul.co.kr">서울EN 바로가기</a>] [<a href="https://www.facebook.com/seoultv/">페이스북</a>] <br/>▶ [<a href="http://en.seoul.co.kr/news/newsList.php?section=starinterN">스타 화보 보기</a>]<br/><br/><br/><br/>ⓒ 서울신문(<a href="http://www.seoul.co.kr">www.seoul.co.kr</a>), 무단전재 및 재배포금지
    					</div>
    <script charset="utf-8" type="text/javascript">
    						enter.read.font.util.init();
    					</script>
    <!-- //본문 내용 - 언론사별 편집된 기사 내용 -->
    </div>
    <!-- //기사 본문 -->
    <!-- 관련 뉴스 -->
    <div class="link_news" id="link_news">
    <h3>
    						서울신문 관련뉴스 <span class="sub_txt"><span class="bar"></span>서울신문 언론사 페이지로 이동합니다.</span>
    </h3>
    <ul>
    <li><a href="http://en.seoul.co.kr/news/newsView.php?id=20170428500061&amp;wlog_tag3=naver_relation" onclick="nclk(this, 'art.cpli', '', '');" target="_blank"><span class="sp_ico"></span>박봄, 공격적인 몸매란 이런 것? "나 돌아갈래"</a></li>
    <li><a href="http://en.seoul.co.kr/news/newsView.php?id=20170501500109&amp;wlog_tag3=naver_relation" onclick="nclk(this, 'art.cpli', '', '');" target="_blank"><span class="sp_ico"></span>전효성 '가슴 작아 고민'이라는 학생에게 한 말이...</a></li>
    <li><a href="http://en.seoul.co.kr/news/newsView.php?id=20170428500083&amp;wlog_tag3=naver_relation" onclick="nclk(this, 'art.cpli', '', '');" target="_blank"><span class="sp_ico"></span>정가은, 딸 소이와 화보 "성형 전 내 눈 닮았다"</a></li>
    <li><a href="http://en.seoul.co.kr/news/newsView.php?id=20170426500161&amp;wlog_tag3=naver_relation" onclick="nclk(this, 'art.cpli', '', '');" target="_blank"><span class="sp_ico"></span>"속옷만 입은듯" 민효린, 시선 강탈한 슬립 원피스 '반전 몸매'</a></li>
    <li><a href="http://en.seoul.co.kr/news/newsView.php?id=20170427500031&amp;wlog_tag3=naver_relation" onclick="nclk(this, 'art.cpli', '', '');" target="_blank"><span class="sp_ico"></span>40세 연하 여자친구와 데이트하는 알 파치노 '과감한 스킨십'</a></li>
    </ul>
    <!-- 섹션 가이드  -->
    <div class="news_section_info">
    <a class="news_section_info_btn" href="#" onclick="return false;"> <span class="ico_guide"></span> <span class="txt_guide">기사 섹션 분류 가이드</span>
    </a>
    <div class="news_section_info_layer">
    <strong class="layer_heading"> <span class="ico_guide"></span>
    <span class="txt_guide">기사 섹션 분류 안내</span>
    </strong>
    <p class="layer_content">
    			개별 기사의 섹션 정보는 해당 언론사의 분류를 따르고 있습니다.
    		</p>
    <a class="news_section_close_btn" href="#" onclick="return false;"> <span class="ico_close"></span> <span class="txt_close blind">안내 레이어 닫기</span>
    </a>
    </div>
    </div>
    </div>
    <!-- //관련 뉴스 -->
    <!-- 하단 공유 버튼 -->
    <div class="btn_social_wrp">
    <!-- 버튼 -->
    <div class="btn_area">
    <div class="btn_like">
    <div class="inner">
    <div class="u_likeit_module">
    <a class="u_likeit_btn" href="#" onclick="nclk(this, 'art.like', '', '');return false"><span class="u_ico"></span><em class="u_cnt"></em></a>
    </div>
    </div>
    </div>
    <div class="sns_area">
    <a class="naver-splugin" data-oninitialize="onSubscribe(this);" data-style="unity" href="#" id="spiButton" onclick="nclk(this, 'art.share', '', '');" title="보내기">
    <span class="inner_r naver-splugin-c"><span class="sp_ico naver-splugin-c"></span></span>
    </a>
    </div>
    </div>
    <!-- //버튼 -->
    </div>
    <!-- //하단 공유 버튼 -->
    <!-- 연관 정보 -->
    <!-- 클러스터링 관련기사 -->
    <div class="end_ad">
    <iframe align="center" border="0" data-veta-preview="p_entertain_end" frameborder="0" height="0" id="f540100" marginheight="0" marginwidth="0" name="f540100" scrolling="no" src="http://ad.naver.com/fxshow?su=SU10084" title="배너광고" width="540"></iframe>
    </div>
    <div class="entertain_cbox">
    <div class="u_cbox" id="cbox_module"></div>
    </div>
    </div>
    </div>
    <!-- //기사 본문 -->
    <!-- 본문 우측영역 -->
    <div class="aside">
    <div class="ad_area">
    <h4 class="blind">광고</h4>
    <div id="da_300250" name="da_300250"><iframe align="center" data-veta-preview="ent_home_01" frameborder="0" height="250" id="f300250" marginheight="0" marginwidth="0" name="f300250" scrolling="no" src="http://ad.naver.com/fxshow?su=SU10049" title="광고" width="300"></iframe></div>
    </div>
    <div class="box _inc_ranking" id="ranking_news">
    <div class="ranking_area">
    <!-- tab top -->
    <div class="tab_top_area">
    <h2 class="tit_ranking"><span class="blind">많이 본 뉴스</span></h2>
    <ul class="tab_lst">
    <li><a class="tab_btn_enter on tabManager" href="#" onclick="nclk(this, 'com.ret', '', '');"><span class="blind">TV연예</span></a></li>
    <li><a class="tab_btn_all tabManager" href="#" onclick="nclk(this, 'com.net', '', '');"><span class="blind">종합</span></a></li>
    </ul>
    </div>
    <!-- //tab top -->
    <div class="tabData">
    <div class="rank_lst">
    <h3 class="blind">많이 본 TV연예 뉴스 선택</h3>
    <ul>
    <li>
    <a alt='[직격인터뷰] 이파니 "남편 서성민, 운명의 남자..저 정말 열심히 살게요"' href="/ranking/read?oid=213&amp;aid=0000967125" onclick="nclk(this, 'com.relist', '', '');"><em class="num no1"><span class="blind">1</span></em>[직격인터뷰] 이파니 "남편 서성민, 운명의 남자..저 정말 열심히 살게요"</a>
    </li>
    <li>
    <a alt="'프듀101' 과열 팬심·편집논란 '몸살'…견제투표에 팬광고까지" href="/ranking/read?oid=001&amp;aid=0009296415" onclick="nclk(this, 'com.relist', '', '');"><em class="num no2"><span class="blind">2</span></em>'프듀101' 과열 팬심·편집논란 '몸살'…견제투표에 팬광고까지</a>
    </li>
    <li>
    <a alt='[Oh!커피 한 잔②] 이정재 "드라마 안하냐고? 출연 제안 많지 않아"' href="/ranking/read?oid=109&amp;aid=0003545549" onclick="nclk(this, 'com.relist', '', '');"><em class="num no3"><span class="blind">3</span></em>[Oh!커피 한 잔②] 이정재 "드라마 안하냐고? 출연 제안 많지 않아"</a>
    </li>
    <li>
    <a alt="[엑's 인터뷰] 김영철 &quot;'따르릉'으로 소원성취, 글로벌하게 터졌으면&quot;" href="/ranking/read?oid=311&amp;aid=0000738407" onclick="nclk(this, 'com.relist', '', '');"><em class="num no4"><span class="blind">4</span></em>[엑's 인터뷰] 김영철 "'따르릉'으로 소원성취, 글로벌하게 터졌으면"</a>
    </li>
    <li>
    <a alt="[할리웃통신] 미란다 커♥에반 스피겔, 부부됐다…LA서 비밀리 웨딩" href="/ranking/read?oid=213&amp;aid=0000967105" onclick="nclk(this, 'com.relist', '', '');"><em class="num no5"><span class="blind">5</span></em>[할리웃통신] 미란다 커♥에반 스피겔, 부부됐다…LA서 비밀리 웨딩</a>
    </li>
    <li>
    <a alt="[N1★초점] 김희철·이상민·서장훈, 어느새 예능 '중심'에" href="/ranking/read?oid=421&amp;aid=0002755744" onclick="nclk(this, 'com.relist', '', '');"><em class="num no6"><span class="blind">6</span></em>[N1★초점] 김희철·이상민·서장훈, 어느새 예능 '중심'에</a>
    </li>
    <li>
    <a alt="[美친box] '캐리비안의 해적5'도 통했다..시리즈 전편 4일만 100만 돌파" href="/ranking/read?oid=109&amp;aid=0003545393" onclick="nclk(this, 'com.relist', '', '');"><em class="num no7"><span class="blind">7</span></em>[美친box] '캐리비안의 해적5'도 통했다..시리즈 전편 4일만 100만 돌파</a>
    </li>
    <li>
    <a alt="'인가' 트와이스, 1위로 5관왕 달성..세븐틴·아이콘 컴백(종합)" href="/ranking/read?oid=112&amp;aid=0002924436" onclick="nclk(this, 'com.relist', '', '');"><em class="num no8"><span class="blind">8</span></em>'인가' 트와이스, 1위로 5관왕 달성..세븐틴·아이콘 컴백(종합)</a>
    </li>
    <li>
    <a alt="[할리웃POP]졸리, 이혼 후 첫 가족 외출..되찾은 미소" href="/ranking/read?oid=112&amp;aid=0002924421" onclick="nclk(this, 'com.relist', '', '');"><em class="num no9"><span class="blind">9</span></em>[할리웃POP]졸리, 이혼 후 첫 가족 외출..되찾은 미소</a>
    </li>
    <li>
    <a alt="“공채 개그맨 ○○○입니다”…1년만에 끝나는 일장춘몽인가" href="/ranking/read?oid=028&amp;aid=0002366255" onclick="nclk(this, 'com.relist', '', '');"><em class="num no10"><span class="blind">10</span></em>“공채 개그맨 ○○○입니다”…1년만에 끝나는 일장춘몽인가</a>
    </li>
    </ul>
    </div>
    <a alt="더보기" class="btn_menu_more" href="http://entertain.naver.com/ranking" onclick="nclk(this, 'com.rmore', '', '');"><span class="sp_ico"></span>더보기</a>
    </div>
    <div class="tabData" style="display:none;">
    <div class="rank_lst">
    <h3 class="blind">많이 본 종합 뉴스 선택</h3>
    <ul>
    <li>
    <a alt='靑 "朴 탄핵 때 사용 특수활동비 30억 혼자 안 썼다"' href="http://news.naver.com/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=079&amp;aid=0002971631" onclick="nclk(this, 'com.rnlist', '', '');"><em class="num no1"><span class="blind">1</span></em>靑 "朴 탄핵 때 사용 특수활동비 30억 혼자 안 썼다"</a>
    </li>
    <li>
    <a alt="주민번호 뒷자리 30일부터 변경 가능…성별은 제외" href="http://news.naver.com/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=001&amp;aid=0009296647" onclick="nclk(this, 'com.rnlist', '', '');"><em class="num no2"><span class="blind">2</span></em>주민번호 뒷자리 30일부터 변경 가능…성별은 제외</a>
    </li>
    <li>
    <a alt="&quot;이낙연도 못 피했다&quot;…文정부서도 이어진 첫 총리 '수난사'" href="http://news.naver.com/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=421&amp;aid=0002755777" onclick="nclk(this, 'com.rnlist', '', '');"><em class="num no3"><span class="blind">3</span></em>"이낙연도 못 피했다"…文정부서도 이어진 첫 총리 '수난사'</a>
    </li>
    <li>
    <a alt='"왜 느리게 가?" 앞차 들이받고 운전자 폭행한 30대' href="http://news.naver.com/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=003&amp;aid=0007979967" onclick="nclk(this, 'com.rnlist', '', '');"><em class="num no4"><span class="blind">4</span></em>"왜 느리게 가?" 앞차 들이받고 운전자 폭행한 30대</a>
    </li>
    <li>
    <a alt="필리핀 계엄령 소도시 교전 지속…두테르테, 반군에 대화제의" href="http://news.naver.com/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=001&amp;aid=0009296469" onclick="nclk(this, 'com.rnlist', '', '');"><em class="num no5"><span class="blind">5</span></em>필리핀 계엄령 소도시 교전 지속…두테르테, 반군에 대화제의</a>
    </li>
    <li>
    <a alt='"코딩만 배우지 마세요"…코딩 열풍 속 회의론도 제기' href="http://news.naver.com/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=366&amp;aid=0000370854" onclick="nclk(this, 'com.rnlist', '', '');"><em class="num no6"><span class="blind">6</span></em>"코딩만 배우지 마세요"…코딩 열풍 속 회의론도 제기</a>
    </li>
    <li>
    <a alt="G7정상회의, '역대 최악' 분위기속 폐막…트럼프가 몰고 온 분열" href="http://news.naver.com/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=018&amp;aid=0003835847" onclick="nclk(this, 'com.rnlist', '', '');"><em class="num no7"><span class="blind">7</span></em>G7정상회의, '역대 최악' 분위기속 폐막…트럼프가 몰고 온 분열</a>
    </li>
    <li>
    <a alt="서울 아파트 시장 '이상 과열 조짐' 왜 이러나" href="http://news.naver.com/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=001&amp;aid=0009296452" onclick="nclk(this, 'com.rnlist', '', '');"><em class="num no8"><span class="blind">8</span></em>서울 아파트 시장 '이상 과열 조짐' 왜 이러나</a>
    </li>
    <li>
    <a alt="손주들 생각하며 어떻게든 내 선에서 막아야지" href="http://news.naver.com/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=028&amp;aid=0002366230" onclick="nclk(this, 'com.rnlist', '', '');"><em class="num no9"><span class="blind">9</span></em>손주들 생각하며 어떻게든 내 선에서 막아야지</a>
    </li>
    <li>
    <a alt="靑, 오후 상황점검회의 통해 야당 설득…이르면 오늘 차관 인사" href="http://news.naver.com/main/ranking/read.nhn?mid=etc&amp;sid1=111&amp;rankingType=popular_day&amp;oid=079&amp;aid=0002971630" onclick="nclk(this, 'com.rnlist', '', '');"><em class="num no10"><span class="blind">10</span></em>靑, 오후 상황점검회의 통해 야당 설득…이르면 오늘 차관 인사</a>
    </li>
    </ul>
    </div>
    <a alt="더보기" class="btn_menu_more" href="http://news.naver.com/main/ranking/popularDay.nhn" onclick="nclk(this, 'com.rmore', '', '');"><span class="sp_ico"></span>더보기</a>
    </div>
    </div>
    </div>
    <div class="box _inc_cast">
    <div class="starcast_area">
    <h4 class="aside_tit">V-ROOKIE</h4>
    <ul class="starcast_lst ">
    <li class="type_thumb">
    <a alt="최고의 루키들을 눈앞에서 만날 수 있는 기회!" href="http://campaign.naver.com/v/rookie/ko/?tap=rookie_stage" onclick="nclk(this, 'com.cimg', '', '');">
    <span class="thumb">
    <img alt="최고의 루키들을 눈앞에서 만날 수 있는 기회!" onerror="imageErrorByParent1(this, 5)" src="http://mimgnews1.naver.net/image/upload/item/2017/05/18/195047827_Untitled-10.jpg?type=nf300_120_q90"/>
    <span class="thumb_border"></span>
    <span class="play_info size_s">
    </span>
    </span>
    <span class="tx">
    <span class="sp_ico"></span><strong>최고의 루키들을 눈앞에서 만날 수 있는 기회!</strong>
    </span>
    </a>
    </li>
    <li class="type_txt">
    <a alt="루키스테이지가 당신을 초대합니다 [지원하기▶]" class="tx" href="http://naver.me/GKlFlRwl" onclick="nclk(this, '', '', '');"><span class="sp_ico"></span>루키스테이지가 당신을 초대합니다 [지원하기▶]</a>
    </li>
    </ul>
    <a alt="더보기" class="btn_menu_more" href="http://campaign.naver.com/v/rookie/ko/?tap=rookie_stage" onclick="nclk(this, 'com.cmore', '', '');"><span class="sp_ico"></span>더보기</a>
    </div>
    </div>
    <div class="box _inc_vlive" id="rightBannerRolling1017109">
    <div class="vlive_slide">
    <div class="slide_area">
    <h4 class="aside_tit">CHANNEL+ GRAND OPEN</h4>
    <div class="vlive_banner">
    <ul class="slide_lst">
    <div>
    <div class="slide image_box">
    <a alt="Apink 채널+의 멤버가 되어주세요!" class="thumb_inner" href="http://www.vlive.tv/channelplus/E27397" onclick="nclk(this, 'com.rbimg', '', '');">
    <span class="thumb">
    <img alt="Apink 채널+의 멤버가 되어주세요!" height="120" onerror="imageErrorByParent1(this, 8)" src="http://mimgnews1.naver.net/image/upload/item/2017/04/28/151056868_03.jpg" width="300"/>
    <span class="thumb_border"></span>
    </span>
    <span class="play_info size_s">
    </span>
    </a>
    <p class="txt">
    <a alt="Apink 채널+의 멤버가 되어주세요!" href="http://www.vlive.tv/channelplus/E27397" onclick="nclk(this, 'com.rbtitle', '', '');">Apink 채널+의 멤버가 되어주세요!</a>
    </p>
    </div>
    </div>
    <div style="display:none;">
    <div class="slide image_box">
    <a alt="LOVELYZ 채널+의 멤버가 되어주세요!" class="thumb_inner" href="http://www.vlive.tv/channelplus/E203A5" onclick="nclk(this, 'com.rbimg', '', '');">
    <span class="thumb">
    <img alt="LOVELYZ 채널+의 멤버가 되어주세요!" height="120" onerror="imageErrorByParent1(this, 8)" src="http://mimgnews1.naver.net/image/upload/item/2017/04/26/162616650_120.jpg" width="300"/>
    <span class="thumb_border"></span>
    </span>
    <span class="play_info size_s">
    </span>
    </a>
    <p class="txt">
    <a alt="LOVELYZ 채널+의 멤버가 되어주세요!" href="http://www.vlive.tv/channelplus/E203A5" onclick="nclk(this, 'com.rbtitle', '', '');">LOVELYZ 채널+의 멤버가 되어주세요!</a>
    </p>
    </div>
    </div>
    <div style="display:none;">
    <div class="slide image_box">
    <a alt="WINNER 채널+의 멤버가 되어주세요!" class="thumb_inner" href="https://goo.gl/5sy2Jx" onclick="nclk(this, 'com.rbimg', '', '');">
    <span class="thumb">
    <img alt="WINNER 채널+의 멤버가 되어주세요!" height="120" onerror="imageErrorByParent1(this, 8)" src="http://mimgnews1.naver.net/image/upload/item/2017/03/28/010712914_56.jpg" width="300"/>
    <span class="thumb_border"></span>
    </span>
    <span class="play_info size_s">
    </span>
    </a>
    <p class="txt">
    <a alt="WINNER 채널+의 멤버가 되어주세요!" href="https://goo.gl/5sy2Jx" onclick="nclk(this, 'com.rbtitle', '', '');">WINNER 채널+의 멤버가 되어주세요!</a>
    </p>
    </div>
    </div>
    <div style="display:none;">
    <div class="slide image_box">
    <a alt="B1A4 채널+의 멤버가 되어주세요!" class="thumb_inner" href="http://www.vlive.tv/channelplus/E40365" onclick="nclk(this, 'com.rbimg', '', '');">
    <span class="thumb">
    <img alt="B1A4 채널+의 멤버가 되어주세요!" height="120" onerror="imageErrorByParent1(this, 8)" src="http://mimgnews1.naver.net/image/upload/item/2017/03/27/140938585_Untitled-3.jpg" width="300"/>
    <span class="thumb_border"></span>
    </span>
    <span class="play_info size_s">
    </span>
    </a>
    <p class="txt">
    <a alt="B1A4 채널+의 멤버가 되어주세요!" href="http://www.vlive.tv/channelplus/E40365" onclick="nclk(this, 'com.rbtitle', '', '');">B1A4 채널+의 멤버가 되어주세요!</a>
    </p>
    </div>
    </div>
    <div style="display:none;">
    <div class="slide image_box">
    <a alt="여자친구 채널+의 멤버가 되어주세요!" class="thumb_inner" href="http://www.vlive.tv/channelplus/E5C32D" onclick="nclk(this, 'com.rbimg', '', '');">
    <span class="thumb">
    <img alt="여자친구 채널+의 멤버가 되어주세요!" height="120" onerror="imageErrorByParent1(this, 8)" src="http://mimgnews1.naver.net/image/upload/item/2017/02/26/153711505_gf.jpg" width="300"/>
    <span class="thumb_border"></span>
    </span>
    <span class="play_info size_s">
    </span>
    </a>
    <p class="txt">
    <a alt="여자친구 채널+의 멤버가 되어주세요!" href="http://www.vlive.tv/channelplus/E5C32D" onclick="nclk(this, 'com.rbtitle', '', '');">여자친구 채널+의 멤버가 되어주세요!</a>
    </p>
    </div>
    </div>
    <div style="display:none;">
    <div class="slide image_box">
    <a alt="비투비 채널+의 멤버가 되어주세요!" class="thumb_inner" href="http://www.vlive.tv/channelplus/E5D32B" onclick="nclk(this, 'com.rbimg', '', '');">
    <span class="thumb">
    <img alt="비투비 채널+의 멤버가 되어주세요!" height="120" onerror="imageErrorByParent1(this, 8)" src="http://mimgnews1.naver.net/image/upload/item/2017/02/25/130340660_03.jpg" width="300"/>
    <span class="thumb_border"></span>
    </span>
    <span class="play_info size_s">
    </span>
    </a>
    <p class="txt">
    <a alt="비투비 채널+의 멤버가 되어주세요!" href="http://www.vlive.tv/channelplus/E5D32B" onclick="nclk(this, 'com.rbtitle', '', '');">비투비 채널+의 멤버가 되어주세요!</a>
    </p>
    </div>
    </div>
    <div style="display:none;">
    <div class="slide image_box">
    <a alt="갓세븐 채널+의 멤버가 되어주세요!" class="thumb_inner" href="http://www.vlive.tv/channelplus/E852DB" onclick="nclk(this, 'com.rbimg', '', '');">
    <span class="thumb">
    <img alt="갓세븐 채널+의 멤버가 되어주세요!" height="120" onerror="imageErrorByParent1(this, 8)" src="http://mimgnews1.naver.net/image/upload/item/2017/02/24/154955408_42.jpg" width="300"/>
    <span class="thumb_border"></span>
    </span>
    <span class="play_info size_s">
    </span>
    </a>
    <p class="txt">
    <a alt="갓세븐 채널+의 멤버가 되어주세요!" href="http://www.vlive.tv/channelplus/E852DB" onclick="nclk(this, 'com.rbtitle', '', '');">갓세븐 채널+의 멤버가 되어주세요!</a>
    </p>
    </div>
    </div>
    <div style="display:none;">
    <div class="slide image_box">
    <a alt="트와이스 채널+의 멤버가 되어주세요!" class="thumb_inner" href="http://www.vlive.tv/channelplus/E832DF" onclick="nclk(this, 'com.rbimg', '', '');">
    <span class="thumb">
    <img alt="트와이스 채널+의 멤버가 되어주세요!" height="120" onerror="imageErrorByParent1(this, 8)" src="http://mimgnews1.naver.net/image/upload/item/2017/01/24/140338306_05.jpg" width="300"/>
    <span class="thumb_border"></span>
    </span>
    <span class="play_info size_s">
    </span>
    </a>
    <p class="txt">
    <a alt="트와이스 채널+의 멤버가 되어주세요!" href="http://www.vlive.tv/channelplus/E832DF" onclick="nclk(this, 'com.rbtitle', '', '');">트와이스 채널+의 멤버가 되어주세요!</a>
    </p>
    </div>
    </div>
    <div style="display:none;">
    <div class="slide image_box">
    <a alt="세븐틴 채널+의 멤버가 되어주세요!" class="thumb_inner" href="http://www.vlive.tv/channelplus/E882D5" onclick="nclk(this, 'com.rbimg', '', '');">
    <span class="thumb">
    <img alt="세븐틴 채널+의 멤버가 되어주세요!" height="120" onerror="imageErrorByParent1(this, 8)" src="http://mimgnews1.naver.net/image/upload/item/2017/01/20/220013999_17.jpg" width="300"/>
    <span class="thumb_border"></span>
    </span>
    <span class="play_info size_s">
    </span>
    </a>
    <p class="txt">
    <a alt="세븐틴 채널+의 멤버가 되어주세요!" href="http://www.vlive.tv/channelplus/E882D5" onclick="nclk(this, 'com.rbtitle', '', '');">세븐틴 채널+의 멤버가 되어주세요!</a>
    </p>
    </div>
    </div>
    <div style="display:none;">
    <div class="slide image_box">
    <a alt="몬스타엑스 채널+의 멤버가 되어주세요!" class="thumb_inner" href="http://www.vlive.tv/channelplus/E862D9" onclick="nclk(this, 'com.rbimg', '', '');">
    <span class="thumb">
    <img alt="몬스타엑스 채널+의 멤버가 되어주세요!" height="120" onerror="imageErrorByParent1(this, 8)" src="http://mimgnews1.naver.net/image/upload/item/2017/01/20/182431322_%25C1%25A6%25B8%25F1-%25BE%25F8%25C0%25BD-2.jpg" width="300"/>
    <span class="thumb_border"></span>
    </span>
    <span class="play_info size_s">
    </span>
    </a>
    <p class="txt">
    <a alt="몬스타엑스 채널+의 멤버가 되어주세요!" href="http://www.vlive.tv/channelplus/E862D9" onclick="nclk(this, 'com.rbtitle', '', '');">몬스타엑스 채널+의 멤버가 되어주세요!</a>
    </p>
    </div>
    </div>
    </ul>
    <span class="btn_arr prev btn_prev">
    <a href="#" onclick="nclk(this, 'com.rbprbev', '', '');">
    <span class="sp_ico">
    <span class="blind">이전</span>
    </span>
    </a>
    </span>
    <span class="btn_arr next btn_next">
    <a href="#" onclick="nclk(this, 'com.rbnext', '', '');">
    <span class="sp_ico">
    <span class="blind">다음</span>
    </span>
    </a>
    </span>
    </div>
    <input class="itemLength" type="hidden" value="9"/>
    <div class="pg_num_area on">
    <a alt="0" class="pg_num num_0 on" href="#" onclick="nclk(this, 'com.rbpage', '', '');">
    <span class="blind">1</span>
    </a>
    <a alt="1" class="pg_num num_1 " href="#" onclick="nclk(this, 'com.rbpage', '', '');">
    <span class="blind">2</span>
    </a>
    <a alt="2" class="pg_num num_2 " href="#" onclick="nclk(this, 'com.rbpage', '', '');">
    <span class="blind">3</span>
    </a>
    <a alt="3" class="pg_num num_3 " href="#" onclick="nclk(this, 'com.rbpage', '', '');">
    <span class="blind">4</span>
    </a>
    <a alt="4" class="pg_num num_4 " href="#" onclick="nclk(this, 'com.rbpage', '', '');">
    <span class="blind">5</span>
    </a>
    <a alt="5" class="pg_num num_5 " href="#" onclick="nclk(this, 'com.rbpage', '', '');">
    <span class="blind">6</span>
    </a>
    <a alt="6" class="pg_num num_6 " href="#" onclick="nclk(this, 'com.rbpage', '', '');">
    <span class="blind">7</span>
    </a>
    <a alt="7" class="pg_num num_7 " href="#" onclick="nclk(this, 'com.rbpage', '', '');">
    <span class="blind">8</span>
    </a>
    <a alt="8" class="pg_num num_8 " href="#" onclick="nclk(this, 'com.rbpage', '', '');">
    <span class="blind">9</span>
    </a>
    <a alt="9" class="pg_num num_9 " href="#" onclick="nclk(this, 'com.rbpage', '', '');">
    <span class="blind">10</span>
    </a>
    </div>
    </div>
    </div>
    </div>
    <div class="box _inc_cast">
    <div class="starcast_area">
    <h4 class="aside_tit">스타캐스트</h4>
    <ul class="starcast_lst special">
    <li class="type_thumb">
    <a alt="&quot;빌(보드)~ 타오르네&quot;…방탄소년단, '쩔어'베가스 25시" href="http://entertain.naver.com/starcast/read?oid=420&amp;aid=0000011359" onclick="nclk(this, 'com.cimg', '', '');">
    <span class="thumb">
    <img alt="&quot;빌(보드)~ 타오르네&quot;…방탄소년단, '쩔어'베가스 25시" onerror="imageErrorByParent1(this, 5)" src="http://mimgnews1.naver.net/image/upload/item/2017/05/26/135932986_003.jpg?type=nf300_120_q90"/>
    <span class="thumb_border"></span>
    <span class="play_info size_s">
    <span class="btn_play"><em class="blind">재생하기</em></span>
    </span>
    </span>
    <span class="tx">
    <span class="sp_ico"></span><strong>"빌(보드)~ 타오르네"…방탄소년단, '쩔어'베가스 25시</strong>
    </span>
    </a>
    </li>
    </ul>
    <a alt="더보기" class="btn_menu_more" href="http://entertain.naver.com/starcast" onclick="nclk(this, 'com.cmore', '', '');"><span class="sp_ico"></span>더보기</a>
    </div>
    </div>
    <div class="box _inc_photo">
    <div class="hot_photo_area">
    <h4 class="aside_tit">연예가 HOT 포토</h4>
    <ul class="hot_photo_lst">
    <li>
    <div class="mid_news">
    <span class="v_mid_box"></span>
    <a alt="엑소 찬열 '많은 사랑 부탁드려요'" class="thumb" href="/photo/issue/863302/100#cid=863302&amp;iid=24629643" onclick="nclk(this, 'com.p3img', '', '');">
    <img alt="엑소 찬열 '많은 사랑 부탁드려요'" onerror="javascript:imageErrorByParent1(this, 1)" src="http://mimgnews1.naver.net/image/origin/421/2017/05/28/2756368.jpg?type=nf124_82_q90"/>
    <span class="thumb_border"></span>
    <span class="sp_ico"></span>
    </a>
    <span class="tit_area">
    <strong><a alt="엑소 찬열 '많은 사랑 부탁드려요'" href="/photo/issue/863302/100#cid=863302&amp;iid=24629643" onclick="nclk(this, 'com.p3art', '', '');">엑소 찬열 '많은 사랑 부탁드려요'</a></strong>
    <em><a alt="엑소 찬열 '많은 사랑 부탁드려요'" href="/photo/issue/863302/100" onclick="nclk(this, 'com.p3tit', '', '');">연예가 현장<span class="sp_ico"></span></a></em>
    </span>
    </div>
    </li>
    <li>
    <div class="mid_news">
    <span class="v_mid_box"></span>
    <a alt="&quot;어린왕자 일병&quot;…슈주 려욱, 입대 8개월차 '꽃미모'" class="thumb" href="/photo/issue/846258/100#cid=846258&amp;iid=1158777" onclick="nclk(this, 'com.p3img', '', '');">
    <img alt="&quot;어린왕자 일병&quot;…슈주 려욱, 입대 8개월차 '꽃미모'" onerror="javascript:imageErrorByParent1(this, 1)" src="http://mimgnews1.naver.net/image/origin/076/2017/05/28/3098831.jpg?type=nf124_82_q90"/>
    <span class="thumb_border"></span>
    <span class="sp_ico"></span>
    </a>
    <span class="tit_area">
    <strong><a alt="&quot;어린왕자 일병&quot;…슈주 려욱, 입대 8개월차 '꽃미모'" href="/photo/issue/846258/100#cid=846258&amp;iid=1158777" onclick="nclk(this, 'com.p3art', '', '');">"어린왕자 일병"…슈주 려욱, 입대 8개…</a></strong>
    <em><a alt="&quot;어린왕자 일병&quot;…슈주 려욱, 입대 8개월차 '꽃미모'" href="/photo/issue/846258/100" onclick="nclk(this, 'com.p3tit', '', '');">스타의 군생활<span class="sp_ico"></span></a></em>
    </span>
    </div>
    </li>
    <li>
    <div class="mid_news">
    <span class="v_mid_box"></span>
    <a alt="한지민, 세상 혼자 사는 미모…일요일에도 비주얼 열일 " class="thumb" href="/photo/issue/845648/100#cid=845648&amp;iid=1158757" onclick="nclk(this, 'com.p3img', '', '');">
    <img alt="한지민, 세상 혼자 사는 미모…일요일에도 비주얼 열일 " onerror="javascript:imageErrorByParent1(this, 1)" src="http://mimgnews1.naver.net/image/origin/117/2017/05/28/2915654.jpg?type=nf124_82_q90"/>
    <span class="thumb_border"></span>
    <span class="sp_ico"></span>
    </a>
    <span class="tit_area">
    <strong><a alt="한지민, 세상 혼자 사는 미모…일요일에도 비주얼 열일 " href="/photo/issue/845648/100#cid=845648&amp;iid=1158757" onclick="nclk(this, 'com.p3art', '', '');">한지민, 세상 혼자 사는 미모…일요일…</a></strong>
    <em><a alt="한지민, 세상 혼자 사는 미모…일요일에도 비주얼 열일 " href="/photo/issue/845648/100" onclick="nclk(this, 'com.p3tit', '', '');">스타들의 일상<span class="sp_ico"></span></a></em>
    </span>
    </div>
    </li>
    <li>
    <div class="mid_news">
    <span class="v_mid_box"></span>
    <a alt='"육빙구의 미소" 육성재, 힐링 타임 소년' class="thumb" href="/photo/issue/852732/100#cid=852732&amp;iid=24629569" onclick="nclk(this, 'com.p3img', '', '');">
    <img alt='"육빙구의 미소" 육성재, 힐링 타임 소년' onerror="javascript:imageErrorByParent1(this, 1)" src="http://mimgnews1.naver.net/image/origin/213/2017/05/28/967133.jpg?type=nf124_82_q90"/>
    <span class="thumb_border"></span>
    <span class="sp_ico"></span>
    </a>
    <span class="tit_area">
    <strong><a alt='"육빙구의 미소" 육성재, 힐링 타임 소년' href="/photo/issue/852732/100#cid=852732&amp;iid=24629569" onclick="nclk(this, 'com.p3art', '', '');">"육빙구의 미소" 육성재, 힐링 타임 소…</a></strong>
    <em><a alt='"육빙구의 미소" 육성재, 힐링 타임 소년' href="/photo/issue/852732/100" onclick="nclk(this, 'com.p3tit', '', '');">스타 얼굴이 궁금해<span class="sp_ico"></span></a></em>
    </span>
    </div>
    </li>
    </ul>
    </div>
    </div>
    <div class="box _inc_major" id="main_news">
    <div class="major_area">
    <!-- tab top -->
    <div class="tab_top_area">
    <h2 class="tit_major"><span class="blind">분야별 주요뉴스</span></h2>
    <!-- [D] 선택된 tab a에 on class 추가 -->
    <ul class="tab_lst">
    <li><a alt="TV연예" class="tab_btn_enter on tabManager" href="#" onclick="nclk(this, 'com.met', '', '');"><span class="blind">TV연예</span></a></li>
    <li><a alt="종합" class="tab_btn_all tabManager" href="#" onclick="nclk(this, 'com.mnt', '', '');"><span class="blind">종합</span></a></li>
    <li><a alt="스포츠" class="tab_btn_spts tabManager" href="#" onclick="nclk(this, 'com.mst', '', '');"><span class="blind">스포츠</span></a></li>
    </ul>
    </div>
    <!-- //tab top -->
    <div class="tabData">
    <div class="major_lst">
    <h3 class="blind">TV연예 주요뉴스 선택</h3>
    <ul>
    <li>
    <a alt="김영철 &quot;'따르릉'으로 소원성취, 글로벌하게 터졌으면&quot;" href="http://entertain.naver.com/read?oid=311&amp;aid=0000738407" onclick="nclk(this, 'com.me1', '', '');"><span class="sp_ico"></span>김영철 "'따르릉'으로 소원성취, 글로벌하게 터졌으면"</a>
    </li>
    <li>
    <a alt="'프듀101' 과열 팬심·편집논란…견제투표에 팬광고까지" href="http://entertain.naver.com/read?oid=001&amp;aid=0009296415" onclick="nclk(this, 'com.me1', '', '');"><span class="sp_ico"></span><strong>'프듀101' 과열 팬심·편집논란…견제투표에 팬광고까지</strong></a>
    </li>
    <li>
    <a alt=' 이파니 "남편 서성민, 운명의 남자…저 정말 열심히 살게요"' href="http://entertain.naver.com/read?oid=213&amp;aid=0000967125" onclick="nclk(this, 'com.me1', '', '');"><span class="sp_ico"></span> 이파니 "남편 서성민, 운명의 남자…저 정말 열심히 살게요"</a>
    </li>
    <li>
    <a alt="'잠실 입성' 받고 '5연속 대상, 엑소의 '신기록'은 계속 된다  " href="http://entertain.naver.com/read?oid=109&amp;aid=0003545711" onclick="nclk(this, 'com.me1', '', '');"><span class="sp_ico"></span>'잠실 입성' 받고 '5연속 대상, 엑소의 '신기록'은 계속 된다  </a>
    </li>
    <li>
    <a alt=" 김희철·이상민·서장훈, 어느새 예능 '중심'에" href="http://entertain.naver.com/read?oid=421&amp;aid=0002755744" onclick="nclk(this, 'com.me1', '', '');"><span class="sp_ico"></span> 김희철·이상민·서장훈, 어느새 예능 '중심'에</a>
    </li>
    </ul><ul>
    <li>
    <a alt="10주년 FT아일랜드, '사랑앓이' 재탄생(With 김나영)" href="http://entertain.naver.com/read?oid=109&amp;aid=0003545497" onclick="nclk(this, 'com.me2', '', '');"><span class="sp_ico"></span>10주년 FT아일랜드, '사랑앓이' 재탄생(With 김나영)</a>
    </li>
    <li>
    <a alt='이정재 "드라마 안하냐고? 출연 제안 많지 않아"' href="http://entertain.naver.com/read?oid=109&amp;aid=0003545549" onclick="nclk(this, 'com.me2', '', '');"><span class="sp_ico"></span><strong>이정재 "드라마 안하냐고? 출연 제안 많지 않아"</strong></a>
    </li>
    <li>
    <a alt="'아는 형님' 오현경, 지금껏 본 적 없는 '新 예능캐릭터' 탄생" href="http://entertain.naver.com/read?oid=076&amp;aid=0003098601" onclick="nclk(this, 'com.me2', '', '');"><span class="sp_ico"></span>'아는 형님' 오현경, 지금껏 본 적 없는 '新 예능캐릭터' 탄생</a>
    </li>
    <li>
    <a alt="'그후' 권해효 &quot;홍상수 감독에게 고맙고, 짜릿했다&quot; " href="http://entertain.naver.com/read?oid=025&amp;aid=0002720376" onclick="nclk(this, 'com.me2', '', '');"><span class="sp_ico"></span>'그후' 권해효 "홍상수 감독에게 고맙고, 짜릿했다" </a>
    </li>
    <li>
    <a alt=" 미란다 커♥에반 스피겔, 부부됐다…LA서 비밀리 웨딩" href="http://entertain.naver.com/read?oid=213&amp;aid=0000967105" onclick="nclk(this, 'com.me2', '', '');"><span class="sp_ico"></span> 미란다 커♥에반 스피겔, 부부됐다…LA서 비밀리 웨딩</a>
    </li>
    </ul><ul>
    <li>
    <a alt=" 목정남X미스터멍…'무도' 하드캐리한 배정남·크러쉬" href="http://entertain.naver.com/read?oid=109&amp;aid=0003545408" onclick="nclk(this, 'com.me3', '', '');"><span class="sp_ico"></span> 목정남X미스터멍…'무도' 하드캐리한 배정남·크러쉬</a>
    </li>
    <li>
    <a alt="'인가' 트와이스, 1위로 5관왕 달성…세븐틴·아이콘 컴백" href="http://entertain.naver.com/read?oid=112&amp;aid=0002924436" onclick="nclk(this, 'com.me3', '', '');"><span class="sp_ico"></span><strong>'인가' 트와이스, 1위로 5관왕 달성…세븐틴·아이콘 컴백</strong></a>
    </li>
    <li>
    <a alt="트와이스·방탄소년단, 5월 아이돌 브랜드평판 1·2위" href="http://entertain.naver.com/read?oid=112&amp;aid=0002924386" onclick="nclk(this, 'com.me3', '', '');"><span class="sp_ico"></span>트와이스·방탄소년단, 5월 아이돌 브랜드평판 1·2위</a>
    </li>
    <li>
    <a alt="'캐리비안5'도 통했다…시리즈 전편 4일만 100만 돌파" href="http://entertain.naver.com/read?oid=109&amp;aid=0003545393" onclick="nclk(this, 'com.me3', '', '');"><span class="sp_ico"></span>'캐리비안5'도 통했다…시리즈 전편 4일만 100만 돌파</a>
    </li>
    <li>
    <a alt=" '당신은' 엄정화·장희진의 기구한 인연, 고부관계될까 " href="http://entertain.naver.com/read?oid=311&amp;aid=0000738382" onclick="nclk(this, 'com.me3', '', '');"><span class="sp_ico"></span> '당신은' 엄정화·장희진의 기구한 인연, 고부관계될까 </a>
    </li>
    </ul>
    </div>
    <a alt="더보기" class="btn_menu_more" href="http://entertain.naver.com/home/mainNews" onclick="nclk(this, 'com.mmore', '', '');"><span class="sp_ico"></span>더보기</a>
    </div>
    <div class="tabData" style="display:none;">
    <div class="major_lst">
    <h3 class="blind">종합 주요뉴스 선택</h3>
    <ul>
    <li>
    <a alt="종합 주요뉴스 0" href=" http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=100&amp;oid=001&amp;aid=0009297168 " onclick="nclk(this, 'com.mn1', '', '');"><span class="sp_ico"></span> '野자극할라'…文대통령, 총리 인준 앞두고 조각 속도조절 </a>
    </li>
    <li>
    <a alt="종합 주요뉴스 1" href=" http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=100&amp;oid=001&amp;aid=0009297264 " onclick="nclk(this, 'com.mn1', '', '');"><span class="sp_ico"></span><strong> '이낙연 인준안' 내일 처리 불투명…대치정국 장기화 가능성도 </strong></a>
    </li>
    <li>
    <a alt="종합 주요뉴스 2" href=" http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=100&amp;oid=008&amp;aid=0003879211 " onclick="nclk(this, 'com.mn1', '', '');"><span class="sp_ico"></span> 서훈 국정원 후보자 "국정원 댓글 알바 사건 재수사" 시사 </a>
    </li>
    <li>
    <a alt="종합 주요뉴스 3" href=" http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=100&amp;oid=052&amp;aid=0001015174 " onclick="nclk(this, 'com.mn1', '', '');"><span class="sp_ico"></span> 與 "협치 요청"·野, 파상공세…청문회 정국 분수령 </a>
    </li>
    <li>
    <a alt="종합 주요뉴스 4" href=" http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=100&amp;oid=001&amp;aid=0009297057 " onclick="nclk(this, 'com.mn1', '', '');"><span class="sp_ico"></span> 국정委 "고위공직자 임용기준안·청문회 개선방안 마련할 것"(종합) </a>
    </li>
    </ul><ul>
    <li>
    <a alt="종합 주요뉴스 5" href=" http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=102&amp;oid=022&amp;aid=0003177113 " onclick="nclk(this, 'com.mn2', '', '');"><span class="sp_ico"></span> [이슈&amp;현장] "세월호 이후에도 달라진 게 없다" 스텔라데이지호 실종자 가족의 절규 </a>
    </li>
    <li>
    <a alt="종합 주요뉴스 6" href=" http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=104&amp;oid=003&amp;aid=0007980532 " onclick="nclk(this, 'com.mn2', '', '');"><span class="sp_ico"></span><strong> 험악했던 G7 회의장…새벽 2시까지 회의에도 美 설득 실패 </strong></a>
    </li>
    <li>
    <a alt="종합 주요뉴스 7" href=" http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=104&amp;oid=003&amp;aid=0007979964 " onclick="nclk(this, 'com.mn2', '', '');"><span class="sp_ico"></span> 영국 경찰, 맨체스터 테러범 범행 직전 CCTV 장면 공개 </a>
    </li>
    <li>
    <a alt="종합 주요뉴스 8" href=" http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=104&amp;oid=003&amp;aid=0007979885 " onclick="nclk(this, 'com.mn2', '', '');"><span class="sp_ico"></span> 중국, G7 정상선언 '동·남중국해 우려'에 강력히 반발 </a>
    </li>
    <li>
    <a alt="종합 주요뉴스 9" href=" http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=104&amp;oid=032&amp;aid=0002791318 " onclick="nclk(this, 'com.mn2', '', '');"><span class="sp_ico"></span> 메시지도 감동도 없었다…'무례한 악수'만 남긴 트럼프의 첫 해외순방 </a>
    </li>
    </ul><ul>
    <li>
    <a alt="종합 주요뉴스 10" href=" http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=101&amp;oid=028&amp;aid=0002366263 " onclick="nclk(this, 'com.mn3', '', '');"><span class="sp_ico"></span>  강남권에서 발화된 아파트값 급등세… 정부 '규제의 칼' 꺼내들까?   </a>
    </li>
    <li>
    <a alt="종합 주요뉴스 11" href=" http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=101&amp;oid=028&amp;aid=0002366260 " onclick="nclk(this, 'com.mn3', '', '');"><span class="sp_ico"></span><strong>  일자리 상황판 속 비정규직 비중 '32.8%'가 전부인가요?  </strong></a>
    </li>
    <li>
    <a alt="종합 주요뉴스 12" href=" http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=101&amp;oid=052&amp;aid=0001015184 " onclick="nclk(this, 'com.mn3', '', '');"><span class="sp_ico"></span> '일단 사고 보자' 반품 급증 …30·40대 여성이 절반 </a>
    </li>
    <li>
    <a alt="종합 주요뉴스 13" href=" http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=101&amp;oid=421&amp;aid=0002755864 " onclick="nclk(this, 'com.mn3', '', '');"><span class="sp_ico"></span> 1조 돌파한 P2P 대출…투자 한도 '연 1천만원'으로 제한 </a>
    </li>
    <li>
    <a alt="종합 주요뉴스 14" href=" http://news.naver.com/main/read.nhn?mode=LSD&amp;mid=shm&amp;sid1=101&amp;oid=015&amp;aid=0003774422 " onclick="nclk(this, 'com.mn3', '', '');"><span class="sp_ico"></span> 2분기도 '깜짝 실적' 코스피 '고공비행' 계속된다 </a>
    </li>
    </ul>
    </div>
    <a alt="더보기" class="btn_menu_more" href="http://news.naver.com/main/hotissue/dailyList.nhn?sid1=000" onclick="nclk(this, 'com.mmore', '', '');"><span class="sp_ico"></span>더보기</a>
    </div>
    <div class="tabData" style="display:none;">
    <div class="major_lst">
    <h3 class="blind">스포츠 주요뉴스 선택</h3>
    <ul>
    <li>
    <a alt="스포츠 주요뉴스 0" href="http://sports.news.naver.com/kfootball/news/read.nhn?oid=139&amp;aid=0002075437" onclick="nclk(this, 'com.ms1', '', '');"><span class="sp_ico"></span>선배들의 응원 "주눅 들지 말고 즐겨라"</a>
    </li>
    <li>
    <a alt="스포츠 주요뉴스 1" href="http://sports.news.naver.com/wbaseball/news/read.nhn?oid=460&amp;aid=0000000598" onclick="nclk(this, 'com.ms1', '', '');"><span class="sp_ico"></span><strong>[오늘의 MLB] 볼티모어 6연패, 김현수는 결장</strong></a>
    </li>
    <li>
    <a alt="스포츠 주요뉴스 2" href="http://sports.news.naver.com/esports/news/read.nhn?oid=442&amp;aid=0000058399" onclick="nclk(this, 'com.ms1', '', '');"><span class="sp_ico"></span>'달라진 모습' IDEPS 3:2 스코어로 승자조 진출</a>
    </li>
    <li>
    <a alt="스포츠 주요뉴스 3" href="http://sports.news.naver.com/kbaseball/news/read.nhn?oid=117&amp;aid=0002915656" onclick="nclk(this, 'com.ms1', '', '');"><span class="sp_ico"></span>주권, 두산전 4이닝 7피안타 4실점…2승 실패</a>
    </li>
    <li>
    <a alt="스포츠 주요뉴스 4" href="http://sports.news.naver.com/wfootball/news/read.nhn?oid=413&amp;aid=0000050871" onclick="nclk(this, 'com.ms1', '', '');"><span class="sp_ico"></span>'뜨거운 응원' 베트남, 온두라스에 0:2 패배</a>
    </li>
    </ul><ul>
    <li>
    <a alt="스포츠 주요뉴스 5" href="http://sports.news.naver.com/volleyball/news/read.nhn?oid=530&amp;aid=0000001216" onclick="nclk(this, 'com.ms2', '', '');"><span class="sp_ico"></span> 우리가 월드리그를 '직관'해야 하는 이유</a>
    </li>
    <li>
    <a alt="스포츠 주요뉴스 6" href="http://sports.news.naver.com/kfootball/news/read.nhn?oid=452&amp;aid=0000000655" onclick="nclk(this, 'com.ms2', '', '');"><span class="sp_ico"></span><strong> 승부보다 중요한, 이 아름다운 U-20 월드컵</strong></a>
    </li>
    <li>
    <a alt="스포츠 주요뉴스 7" href="http://sports.news.naver.com/general/news/read.nhn?oid=001&amp;aid=0009296561" onclick="nclk(this, 'com.ms2', '', '');"><span class="sp_ico"></span>냉정하게 손 턴 알파고 뜨거운 눈물 흘린 커제</a>
    </li>
    <li>
    <a alt="스포츠 주요뉴스 8" href="http://sports.news.naver.com/basketball/news/read.nhn?oid=351&amp;aid=0000028793" onclick="nclk(this, 'com.ms2', '', '');"><span class="sp_ico"></span>토론토, 서지 이바카와의 재계약에 긍적적!</a>
    </li>
    <li>
    <a alt="스포츠 주요뉴스 9" href="http://sports.news.naver.com/esports/news/read.nhn?oid=236&amp;aid=0000157546" onclick="nclk(this, 'com.ms2', '', '');"><span class="sp_ico"></span>천정희 코치, 中 LSPL 영글로리 합류 </a>
    </li>
    </ul><ul>
    <li>
    <a alt="스포츠 주요뉴스 10" href="http://sports.news.naver.com/basketball/news/read.nhn?oid=398&amp;aid=0000008580" onclick="nclk(this, 'com.ms3', '', '');"><span class="sp_ico"></span>케빈 러브 "언더독? 우리는 디펜딩 챔피언"</a>
    </li>
    <li>
    <a alt="스포츠 주요뉴스 11" href="http://sports.news.naver.com/basketball/news/read.nhn?oid=076&amp;aid=0003098575" onclick="nclk(this, 'com.ms3', '', '');"><span class="sp_ico"></span><strong> '지지부진' 라틀리프 귀화, 불발 가능성↑</strong></a>
    </li>
    <li>
    <a alt="스포츠 주요뉴스 12" href="http://sports.news.naver.com/wbaseball/news/read.nhn?oid=410&amp;aid=0000391714" onclick="nclk(this, 'com.ms3', '', '');"><span class="sp_ico"></span>우리아스, 마이너  첫 등판에서 6이닝 1실점</a>
    </li>
    <li>
    <a alt="스포츠 주요뉴스 13" href="http://sports.news.naver.com/wbaseball/news/read.nhn?oid=109&amp;aid=0003545502" onclick="nclk(this, 'com.ms3', '', '');"><span class="sp_ico"></span>박병호, IND전 6타수 1안타 트리플A 타율 .228</a>
    </li>
    <li>
    <a alt="스포츠 주요뉴스 14" href="http://sports.news.naver.com/basketball/news/read.nhn?oid=351&amp;aid=0000028794" onclick="nclk(this, 'com.ms3', '', '');"><span class="sp_ico"></span>매직 존슨 사장 "잉그램 제외, 트레이드 가능! "</a>
    </li>
    </ul>
    </div>
    <a alt="더보기" class="btn_menu_more" href="http://sports.news.naver.com/" onclick="nclk(this, 'com.mmore', '', '');"><span class="sp_ico"></span>더보기</a>
    </div>
    </div>
    </div>
    <div class="box _inc_vlive" id="rightBannerRolling1013200">
    <div class="vlive_slide">
    <div class="slide_area">
    <h4 class="aside_tit">[V LIVE] V 생중계</h4>
    <div class="vlive_banner">
    <ul class="slide_lst">
    <div>
    <div class="slide image_box">
    <a alt="오늘 11PM 크나큰 X 눕방LieV" class="thumb_inner" href="http://www.vlive.tv/video/31167" onclick="nclk(this, 'com.rbimg', '', '');">
    <span class="thumb">
    <img alt="오늘 11PM 크나큰 X 눕방LieV" height="120" onerror="imageErrorByParent1(this, 8)" src="http://mimgnews1.naver.net/image/upload/item/2017/05/25/113324602_300X120_PC%25BF%25AC%25BF%25B9%25C8%25A8%25BF%25EC%25C7%25CF%25B4%25DC.jpg" width="300"/>
    <span class="thumb_border"></span>
    </span>
    <span class="play_info size_s">
    <span class="btn_play">
    <em class="blind">재생하기</em>
    </span>
    </span>
    </a>
    <p class="txt">
    <a alt="오늘 11PM 크나큰 X 눕방LieV" href="http://www.vlive.tv/video/31167" onclick="nclk(this, 'com.rbtitle', '', '');">오늘 11PM 크나큰 X 눕방LieV</a>
    </p>
    </div>
    </div>
    <div style="display:none;">
    <div class="slide image_box">
    <a alt="5/29(월) 8PM 아스트로 컴백 쇼케이스" class="thumb_inner" href="http://www.vlive.tv/video/30982" onclick="nclk(this, 'com.rbimg', '', '');">
    <span class="thumb">
    <img alt="5/29(월) 8PM 아스트로 컴백 쇼케이스" height="120" onerror="imageErrorByParent1(this, 8)" src="http://mimgnews1.naver.net/image/upload/item/2017/05/24/171212033_300X120.jpg" width="300"/>
    <span class="thumb_border"></span>
    </span>
    <span class="play_info size_s">
    <span class="btn_play">
    <em class="blind">재생하기</em>
    </span>
    </span>
    </a>
    <p class="txt">
    <a alt="5/29(월) 8PM 아스트로 컴백 쇼케이스" href="http://www.vlive.tv/video/30982" onclick="nclk(this, 'com.rbtitle', '', '');">5/29(월) 8PM 아스트로 컴백 쇼케이스</a>
    </p>
    </div>
    </div>
    <div style="display:none;">
    <div class="slide image_box">
    <a alt="5/29(월) 9PM 에이프릴 컴백 라이브" class="thumb_inner" href="http://www.vlive.tv/video/31085" onclick="nclk(this, 'com.rbimg', '', '');">
    <span class="thumb">
    <img alt="5/29(월) 9PM 에이프릴 컴백 라이브" height="120" onerror="imageErrorByParent1(this, 8)" src="http://mimgnews1.naver.net/image/upload/item/2017/05/24/180208896_300X120_PC%25BF%25AC%25BF%25B9%25C8%25A8%25BF%25EC%25C7%25CF%25B4%25DC.jpg" width="300"/>
    <span class="thumb_border"></span>
    </span>
    <span class="play_info size_s">
    </span>
    </a>
    <p class="txt">
    <a alt="5/29(월) 9PM 에이프릴 컴백 라이브" href="http://www.vlive.tv/video/31085" onclick="nclk(this, 'com.rbtitle', '', '');">5/29(월) 9PM 에이프릴 컴백 라이브</a>
    </p>
    </div>
    </div>
    <div style="display:none;">
    <div class="slide image_box">
    <a alt="매주 화요일 7PM &lt;2017 울림PICK&gt;" class="thumb_inner" href="http://www.vlive.tv/video/30955" onclick="nclk(this, 'com.rbimg', '', '');">
    <span class="thumb">
    <img alt="매주 화요일 7PM &lt;2017 울림PICK&gt;" height="120" onerror="imageErrorByParent1(this, 8)" src="http://mimgnews1.naver.net/image/upload/item/2017/05/25/173439076_300X120.jpg" width="300"/>
    <span class="thumb_border"></span>
    </span>
    <span class="play_info size_s">
    </span>
    </a>
    <p class="txt">
    <a alt="매주 화요일 7PM &lt;2017 울림PICK&gt;" href="http://www.vlive.tv/video/30955" onclick="nclk(this, 'com.rbtitle', '', '');">매주 화요일 7PM &lt;2017 울림PICK&gt;</a>
    </p>
    </div>
    </div>
    <div style="display:none;">
    <div class="slide image_box">
    <a alt="매주 금요일 11PM 하이라이트의 첫 리얼리티 " class="thumb_inner" href="http://www.vlive.tv/video/30164" onclick="nclk(this, 'com.rbimg', '', '');">
    <span class="thumb">
    <img alt="매주 금요일 11PM 하이라이트의 첫 리얼리티 " height="120" onerror="imageErrorByParent1(this, 8)" src="http://mimgnews1.naver.net/image/upload/item/2017/05/24/235711763_332X130_PC%25B8%25DE%25C0%25CE%25BF%25EC%25C7%25CF%25B4%25DC.jpg" width="300"/>
    <span class="thumb_border"></span>
    </span>
    <span class="play_info size_s">
    <span class="btn_play">
    <em class="blind">재생하기</em>
    </span>
    </span>
    </a>
    <p class="txt">
    <a alt="매주 금요일 11PM 하이라이트의 첫 리얼리티 " href="http://www.vlive.tv/video/30164" onclick="nclk(this, 'com.rbtitle', '', '');">매주 금요일 11PM 하이라이트의 첫 리얼리티 </a>
    </p>
    </div>
    </div>
    <div style="display:none;">
    <div class="slide image_box">
    <a alt="매일밤 9PM 캐스퍼 라디오 생중계" class="thumb_inner" href="http://channels.vlive.tv/E1C3AD" onclick="nclk(this, 'com.rbimg', '', '');">
    <span class="thumb">
    <img alt="매일밤 9PM 캐스퍼 라디오 생중계" height="120" onerror="imageErrorByParent1(this, 8)" src="http://mimgnews1.naver.net/image/upload/item/2017/05/02/143613515_300X120_PC%25BF%25AC%25BF%25B9%25C8%25A8%25BF%25EC%25C7%25CF%25B4%25DC.jpg" width="300"/>
    <span class="thumb_border"></span>
    </span>
    <span class="play_info size_s">
    <span class="btn_play">
    <em class="blind">재생하기</em>
    </span>
    </span>
    </a>
    <p class="txt">
    <a alt="매일밤 9PM 캐스퍼 라디오 생중계" href="http://channels.vlive.tv/E1C3AD" onclick="nclk(this, 'com.rbtitle', '', '');">매일밤 9PM 캐스퍼 라디오 생중계</a>
    </p>
    </div>
    </div>
    </ul>
    <span class="btn_arr prev btn_prev">
    <a href="#" onclick="nclk(this, 'com.rbprbev', '', '');">
    <span class="sp_ico">
    <span class="blind">이전</span>
    </span>
    </a>
    </span>
    <span class="btn_arr next btn_next">
    <a href="#" onclick="nclk(this, 'com.rbnext', '', '');">
    <span class="sp_ico">
    <span class="blind">다음</span>
    </span>
    </a>
    </span>
    </div>
    <input class="itemLength" type="hidden" value="6"/>
    <div class="pg_num_area on">
    <a alt="0" class="pg_num num_0 on" href="#" onclick="nclk(this, 'com.rbpage', '', '');">
    <span class="blind">1</span>
    </a>
    <a alt="1" class="pg_num num_1 " href="#" onclick="nclk(this, 'com.rbpage', '', '');">
    <span class="blind">2</span>
    </a>
    <a alt="2" class="pg_num num_2 " href="#" onclick="nclk(this, 'com.rbpage', '', '');">
    <span class="blind">3</span>
    </a>
    <a alt="3" class="pg_num num_3 " href="#" onclick="nclk(this, 'com.rbpage', '', '');">
    <span class="blind">4</span>
    </a>
    <a alt="4" class="pg_num num_4 " href="#" onclick="nclk(this, 'com.rbpage', '', '');">
    <span class="blind">5</span>
    </a>
    <a alt="5" class="pg_num num_5 " href="#" onclick="nclk(this, 'com.rbpage', '', '');">
    <span class="blind">6</span>
    </a>
    </div>
    </div>
    </div>
    </div>
    <!-- 투표종료 -->
    <div class="box _inc_issue">
    <div class="issue_area">
    <h4 class="aside_tit">영화계는 지금</h4>
    <div class="top_issue">
    <a alt=" '캐리비안의 해적5'도 통했다…시리즈 전편 4일만 100만 돌파" class="mid_news" href="/topic/999195/999195/read?oid=109&amp;aid=0003545393" onclick="nclk(this, 'com.iimg', '', '');">
    <span class="v_mid_box"></span>
    <span class="thumb">
    <img alt=" '캐리비안의 해적5'도 통했다…시리즈 전편 4일만 100만 돌파" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/origin/109/2017/05/28/3545393.jpg?type=nf124_82_q90"/>
    <span class="thumb_border"></span>
    </span>
    <span class="tit_area">
    <strong> '캐리비안의 해적5'도 통했다…시리즈…</strong>
    <span class="press">OSEN<span><em></em></span></span>
    </span>
    </a>
    </div>
    <ul class="issue_lst">
    <li>
    <a alt="'겟아웃' 개봉 10일만에 150만, '컨저링'보다 빠르다" href="/topic/999195/999195/read?oid=117&amp;aid=0002915528" onclick="nclk(this, 'com.isub', '', '');">
    <span class="sp_ico sub_ico"></span>'겟아웃' 개봉 10일만에 150만, '컨저링'보다 빠르다
    							</a>
    </li>
    <li>
    <a alt="'응답하라 80년대'…할리우드, 80년대 영화 리메이크 붐" href="/topic/999195/999195/read?oid=001&amp;aid=0009296227" onclick="nclk(this, 'com.isub', '', '');">
    <span class="sp_ico sub_ico"></span>'응답하라 80년대'…할리우드, 80년대 영화 리메이크 붐
    							</a>
    </li>
    <li>
    <a alt="'노무현입니다' 주말에도 흥행…3일째 누적관객 38만명" href="/topic/999195/999195/read?oid=001&amp;aid=0009296383" onclick="nclk(this, 'com.isub', '', '');">
    <span class="sp_ico sub_ico"></span>'노무현입니다' 주말에도 흥행…3일째 누적관객 38만명
    							</a>
    </li>
    </ul>
    <a alt="더보기" class="btn_menu_more" href="http://entertain.naver.com/movie/topic" onclick="nclk(this, 'com.imore', '', '');"><span class="sp_ico"></span>더보기</a>
    </div>
    </div>
    <div class="box _inc_issue">
    <div class="issue_area">
    <h4 class="aside_tit">오늘의 뮤직</h4>
    <div class="top_issue">
    <a alt="FT아일랜드, 10주년 맞아 '사랑앓이' 재탄생(With 김나영)" class="mid_news" href="/topic/999390/999390/read?oid=109&amp;aid=0003545497" onclick="nclk(this, 'com.iimg', '', '');">
    <span class="v_mid_box"></span>
    <span class="thumb">
    <img alt="FT아일랜드, 10주년 맞아 '사랑앓이' 재탄생(With 김나영)" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/item/2017/05/28/121154884_inputTemplate_1436427.jpg?type=nf124_82_q90"/>
    <span class="thumb_border"></span>
    </span>
    <span class="tit_area">
    <strong>FT아일랜드, 10주년 맞아 '사랑앓이' …</strong>
    <span class="press">OSEN<span><em></em></span></span>
    </span>
    </a>
    </div>
    <ul class="issue_lst">
    <li>
    <a alt=" 다음주 가요계, 음원퀸 격돌…씨스타vs백아연vs수란" href="/topic/999390/999390/read?oid=109&amp;aid=0003545490" onclick="nclk(this, 'com.isub', '', '');">
    <span class="sp_ico sub_ico"></span> 다음주 가요계, 음원퀸 격돌…씨스타vs백아연vs수란
    							</a>
    </li>
    <li>
    <a alt=" 수란·트와이스, 심상찮은 역주행 롱런…1위 행진" href="/topic/999390/999390/read?oid=109&amp;aid=0003545417" onclick="nclk(this, 'com.isub', '', '');">
    <span class="sp_ico sub_ico"></span> 수란·트와이스, 심상찮은 역주행 롱런…1위 행진
    							</a>
    </li>
    <li>
    <a alt='美 빌보드, 세븐틴 극찬…"가장 혁신적인 팀, 놀랍고도 환영"' href="/topic/999390/999390/read?oid=241&amp;aid=0002676944" onclick="nclk(this, 'com.isub', '', '');">
    <span class="sp_ico sub_ico"></span>美 빌보드, 세븐틴 극찬…"가장 혁신적인 팀, 놀랍고도 환영"
    							</a>
    </li>
    </ul>
    <a alt="더보기" class="btn_menu_more" href="http://entertain.naver.com/music/topic" onclick="nclk(this, 'com.imore', '', '');"><span class="sp_ico"></span>더보기</a>
    </div>
    </div>
    <div class="box" id="rightHotPhotoSlide846241">
    <div class="slide_area">
    <h4 class="aside_tit">화보 속 스타</h4>
    <div class="photo_slide">
    <div class="slide_lst">
    <div>
    <ul class="slide_lst">
    <li>
    <a href="/photo/issue/846241/100#cid=846241&amp;iid=1459155" onclick="nclk(this, 'com.p1img', '', '');">
    <span class="thumb_area">
    <img alt="" onerror="imageErrorByParent1(this, 3)" src="http://mimgnews1.naver.net/image/origin/213/2017/05/27/967030.jpg?type=nf300_180_q90"/>
    <span class="thumb_border"></span>
    </span>
    </a>
    </li>
    </ul>
    <a class="tx" href="/photo/issue/846241/100#cid=846241&amp;iid=1459155" onclick="nclk(this, 'com.p1art', '', '');"><strong>"수컷의 향기" 이정재, 완벽한 남자 포스  </strong></a>
    </div>
    <div style="display:none;">
    <ul class="slide_lst">
    <li>
    <a href="/photo/issue/846241/100#cid=846241&amp;iid=1458869" onclick="nclk(this, 'com.p1img', '', '');">
    <span class="thumb_area">
    <img alt="" onerror="imageErrorByParent1(this, 3)" src="http://mimgnews1.naver.net/image/origin/117/2017/05/27/2915321.jpg?type=nf300_180_q90"/>
    <span class="thumb_border"></span>
    </span>
    </a>
    </li>
    </ul>
    <a class="tx" href="/photo/issue/846241/100#cid=846241&amp;iid=1458869" onclick="nclk(this, 'com.p1art', '', '');"><strong>"반전 섹시미"…한지민, 베이글녀 등극 </strong></a>
    </div>
    <div style="display:none;">
    <ul class="slide_lst">
    <li>
    <a href="/photo/issue/846241/100#cid=846241&amp;iid=1458837" onclick="nclk(this, 'com.p1img', '', '');">
    <span class="thumb_area">
    <img alt="" onerror="imageErrorByParent1(this, 3)" src="http://mimgnews1.naver.net/image/origin/076/2017/05/27/3098399.jpg?type=nf300_180_q90"/>
    <span class="thumb_border"></span>
    </span>
    </a>
    </li>
    </ul>
    <a class="tx" href="/photo/issue/846241/100#cid=846241&amp;iid=1458837" onclick="nclk(this, 'com.p1art', '', '');"><strong>김옥빈 "女 영화 관심…서현진·한효주 호흡하고파"</strong></a>
    </div>
    <div style="display:none;">
    <ul class="slide_lst">
    <li>
    <a href="/photo/issue/846241/100#cid=846241&amp;iid=1458695" onclick="nclk(this, 'com.p1img', '', '');">
    <span class="thumb_area">
    <img alt="" onerror="imageErrorByParent1(this, 3)" src="http://mimgnews1.naver.net/image/origin/213/2017/05/27/966932.jpg?type=nf300_180_q90"/>
    <span class="thumb_border"></span>
    </span>
    </a>
    </li>
    </ul>
    <a class="tx" href="/photo/issue/846241/100#cid=846241&amp;iid=1458695" onclick="nclk(this, 'com.p1art', '', '');"><strong>황치열 "자기관리 잘하는 여자 좋아, 근육女도 OK" </strong></a>
    </div>
    <div style="display:none;">
    <ul class="slide_lst">
    <li>
    <a href="/photo/issue/846241/100#cid=846241&amp;iid=1458153" onclick="nclk(this, 'com.p1img', '', '');">
    <span class="thumb_area">
    <img alt="" onerror="imageErrorByParent1(this, 3)" src="http://mimgnews1.naver.net/image/origin/112/2017/05/26/2924102.jpg?type=nf300_180_q90"/>
    <span class="thumb_border"></span>
    </span>
    </a>
    </li>
    </ul>
    <a class="tx" href="/photo/issue/846241/100#cid=846241&amp;iid=1458153" onclick="nclk(this, 'com.p1art', '', '');"><strong>"이 눈빛, 남심 저격"…박신혜, 몽환의 시크</strong></a>
    </div>
    <div style="display:none;">
    <ul class="slide_lst">
    <li>
    <a href="/photo/issue/846241/100#cid=846241&amp;iid=1458089" onclick="nclk(this, 'com.p1img', '', '');">
    <span class="thumb_area">
    <img alt="" onerror="imageErrorByParent1(this, 3)" src="http://mimgnews1.naver.net/image/origin/076/2017/05/26/3098142.jpg?type=nf300_180_q90"/>
    <span class="thumb_border"></span>
    </span>
    </a>
    </li>
    </ul>
    <a class="tx" href="/photo/issue/846241/100#cid=846241&amp;iid=1458089" onclick="nclk(this, 'com.p1art', '', '');"><strong>데이비드 맥기니스 "송중기와 여전히 연락 주고받는다"</strong></a>
    </div>
    <div style="display:none;">
    <ul class="slide_lst">
    <li>
    <a href="/photo/issue/846241/100#cid=846241&amp;iid=1458081" onclick="nclk(this, 'com.p1img', '', '');">
    <span class="thumb_area">
    <img alt="" onerror="imageErrorByParent1(this, 3)" src="http://mimgnews1.naver.net/image/origin/112/2017/05/26/2924087.jpg?type=nf300_180_q90"/>
    <span class="thumb_border"></span>
    </span>
    </a>
    </li>
    </ul>
    <a class="tx" href="/photo/issue/846241/100#cid=846241&amp;iid=1458081" onclick="nclk(this, 'com.p1art', '', '');"><strong>데이비드 맥기니스 "'태양의 후예' LA 하정우 만나러 갔다 캐스팅"</strong></a>
    </div>
    <div style="display:none;">
    <ul class="slide_lst">
    <li>
    <a href="/photo/issue/846241/100#cid=846241&amp;iid=1458014" onclick="nclk(this, 'com.p1img', '', '');">
    <span class="thumb_area">
    <img alt="" onerror="imageErrorByParent1(this, 3)" src="http://mimgnews1.naver.net/image/origin/076/2017/05/26/3098124.jpg?type=nf300_180_q90"/>
    <span class="thumb_border"></span>
    </span>
    </a>
    </li>
    </ul>
    <a class="tx" href="/photo/issue/846241/100#cid=846241&amp;iid=1458014" onclick="nclk(this, 'com.p1art', '', '');"><strong>"이 몸매, 실화"…에이핑크 손나은, 명불허전 비주얼 </strong></a>
    </div>
    <div style="display:none;">
    <ul class="slide_lst">
    <li>
    <a href="/photo/issue/846241/100#cid=846241&amp;iid=1457925" onclick="nclk(this, 'com.p1img', '', '');">
    <span class="thumb_area">
    <img alt="" onerror="imageErrorByParent1(this, 3)" src="http://mimgnews1.naver.net/image/origin/076/2017/05/26/3098108.jpg?type=nf300_180_q90"/>
    <span class="thumb_border"></span>
    </span>
    </a>
    </li>
    </ul>
    <a class="tx" href="/photo/issue/846241/100#cid=846241&amp;iid=1457925" onclick="nclk(this, 'com.p1art', '', '');"><strong>천단비 "롤모델은 이소라, 아이돌은 엑소 디오 좋아" </strong></a>
    </div>
    <div style="display:none;">
    <ul class="slide_lst">
    <li>
    <a href="/photo/issue/846241/100#cid=846241&amp;iid=1457813" onclick="nclk(this, 'com.p1img', '', '');">
    <span class="thumb_area">
    <img alt="" onerror="imageErrorByParent1(this, 3)" src="http://mimgnews1.naver.net/image/origin/009/2017/05/26/3948476.jpg?type=nf300_180_q90"/>
    <span class="thumb_border"></span>
    </span>
    </a>
    </li>
    </ul>
    <a class="tx" href="/photo/issue/846241/100#cid=846241&amp;iid=1457813" onclick="nclk(this, 'com.p1art', '', '');"><strong>소녀시대 유리, 흑진주의 홀리데이 룩 </strong></a>
    </div>
    <div style="display:none;">
    <ul class="slide_lst">
    <li>
    <a href="/photo/issue/846241/100#cid=846241&amp;iid=1457801" onclick="nclk(this, 'com.p1img', '', '');">
    <span class="thumb_area">
    <img alt="" onerror="imageErrorByParent1(this, 3)" src="http://mimgnews1.naver.net/image/origin/112/2017/05/26/2924007.jpg?type=nf300_180_q90"/>
    <span class="thumb_border"></span>
    </span>
    </a>
    </li>
    </ul>
    <a class="tx" href="/photo/issue/846241/100#cid=846241&amp;iid=1457801" onclick="nclk(this, 'com.p1art', '', '');"><strong>안효섭 "차세대 랜선 남친? 인기 조금씩 느끼는 중"</strong></a>
    </div>
    <div style="display:none;">
    <ul class="slide_lst">
    <li>
    <a href="/photo/issue/846241/100#cid=846241&amp;iid=1457705" onclick="nclk(this, 'com.p1img', '', '');">
    <span class="thumb_area">
    <img alt="" onerror="imageErrorByParent1(this, 3)" src="http://mimgnews1.naver.net/image/origin/112/2017/05/26/2923974.jpg?type=nf300_180_q90"/>
    <span class="thumb_border"></span>
    </span>
    </a>
    </li>
    </ul>
    <a class="tx" href="/photo/issue/846241/100#cid=846241&amp;iid=1457705" onclick="nclk(this, 'com.p1art', '', '');"><strong>"여전히 예뻐" 소녀시대 유리, 싱그러운 매력</strong></a>
    </div>
    <div style="display:none;">
    <ul class="slide_lst">
    <li>
    <a href="/photo/issue/846241/100#cid=846241&amp;iid=1457685" onclick="nclk(this, 'com.p1img', '', '');">
    <span class="thumb_area">
    <img alt="" onerror="imageErrorByParent1(this, 3)" src="http://mimgnews1.naver.net/image/origin/117/2017/05/26/2914868.jpg?type=nf300_180_q90"/>
    <span class="thumb_border"></span>
    </span>
    </a>
    </li>
    </ul>
    <a class="tx" href="/photo/issue/846241/100#cid=846241&amp;iid=1457685" onclick="nclk(this, 'com.p1art', '', '');"><strong>방탄소년단 日 '앙앙' 표지 장식, 스페셜판 동시발매 역대최초</strong></a>
    </div>
    <div style="display:none;">
    <ul class="slide_lst">
    <li>
    <a href="/photo/issue/846241/100#cid=846241&amp;iid=1457651" onclick="nclk(this, 'com.p1img', '', '');">
    <span class="thumb_area">
    <img alt="" onerror="imageErrorByParent1(this, 3)" src="http://mimgnews1.naver.net/image/origin/117/2017/05/26/2914860.jpg?type=nf300_180_q90"/>
    <span class="thumb_border"></span>
    </span>
    </a>
    </li>
    </ul>
    <a class="tx" href="/photo/issue/846241/100#cid=846241&amp;iid=1457651" onclick="nclk(this, 'com.p1art', '', '');"><strong>"오빠 소리 절로 나오네"…여진구, 남성미 폭발 </strong></a>
    </div>
    <div style="display:none;">
    <ul class="slide_lst">
    <li>
    <a href="/photo/issue/846241/100#cid=846241&amp;iid=1457612" onclick="nclk(this, 'com.p1img', '', '');">
    <span class="thumb_area">
    <img alt="" onerror="imageErrorByParent1(this, 3)" src="http://mimgnews1.naver.net/image/origin/112/2017/05/26/2923936.jpg?type=nf300_180_q90"/>
    <span class="thumb_border"></span>
    </span>
    </a>
    </li>
    </ul>
    <a class="tx" href="/photo/issue/846241/100#cid=846241&amp;iid=1457612" onclick="nclk(this, 'com.p1art', '', '');"><strong>'러브 액츄얼리', 14년만의 단체 화보…반가운 얼굴</strong></a>
    </div>
    <div style="display:none;">
    <ul class="slide_lst">
    <li>
    <a href="/photo/issue/846241/100#cid=846241&amp;iid=1457558" onclick="nclk(this, 'com.p1img', '', '');">
    <span class="thumb_area">
    <img alt="" onerror="imageErrorByParent1(this, 3)" src="http://mimgnews1.naver.net/image/origin/023/2017/05/26/3283384.jpg?type=nf300_180_q90"/>
    <span class="thumb_border"></span>
    </span>
    </a>
    </li>
    </ul>
    <a class="tx" href="/photo/issue/846241/100#cid=846241&amp;iid=1457558" onclick="nclk(this, 'com.p1art', '', '');"><strong> 이정신 "하면 할수록 직업 사랑하게 돼…평생 하고파"</strong></a>
    </div>
    <div style="display:none;">
    <ul class="slide_lst">
    <li>
    <a href="/photo/issue/846241/100#cid=846241&amp;iid=1457483" onclick="nclk(this, 'com.p1img', '', '');">
    <span class="thumb_area">
    <img alt="" onerror="imageErrorByParent1(this, 3)" src="http://mimgnews1.naver.net/image/origin/213/2017/05/26/966769.jpg?type=nf300_180_q90"/>
    <span class="thumb_border"></span>
    </span>
    </a>
    </li>
    </ul>
    <a class="tx" href="/photo/issue/846241/100#cid=846241&amp;iid=1457483" onclick="nclk(this, 'com.p1art', '', '');"><strong>"이 오빠, 설렌다" 로버트 패틴슨, 여심 잡는 상남자 </strong></a>
    </div>
    <div style="display:none;">
    <ul class="slide_lst">
    <li>
    <a href="/photo/issue/846241/100#cid=846241&amp;iid=1457442" onclick="nclk(this, 'com.p1img', '', '');">
    <span class="thumb_area">
    <img alt="" onerror="imageErrorByParent1(this, 3)" src="http://mimgnews1.naver.net/image/origin/213/2017/05/26/966763.jpg?type=nf300_180_q90"/>
    <span class="thumb_border"></span>
    </span>
    </a>
    </li>
    </ul>
    <a class="tx" href="/photo/issue/846241/100#cid=846241&amp;iid=1457442" onclick="nclk(this, 'com.p1art', '', '');"><strong>고아성 "내가 진심으로 느낀다면 대중도 좋아해줄 것" </strong></a>
    </div>
    <div style="display:none;">
    <ul class="slide_lst">
    <li>
    <a href="/photo/issue/846241/100#cid=846241&amp;iid=1457440" onclick="nclk(this, 'com.p1img', '', '');">
    <span class="thumb_area">
    <img alt="" onerror="imageErrorByParent1(this, 3)" src="http://mimgnews1.naver.net/image/origin/433/2017/05/26/30466.jpg?type=nf300_180_q90"/>
    <span class="thumb_border"></span>
    </span>
    </a>
    </li>
    </ul>
    <a class="tx" href="/photo/issue/846241/100#cid=846241&amp;iid=1457440" onclick="nclk(this, 'com.p1art', '', '');"><strong>"비주얼이 그림이다"…박민영, 치명적 팜므파탈</strong></a>
    </div>
    <div style="display:none;">
    <ul class="slide_lst">
    <li>
    <a href="/photo/issue/846241/100#cid=846241&amp;iid=1457345" onclick="nclk(this, 'com.p1img', '', '');">
    <span class="thumb_area">
    <img alt="" onerror="imageErrorByParent1(this, 3)" src="http://mimgnews1.naver.net/image/origin/076/2017/05/26/3097931.jpg?type=nf300_180_q90"/>
    <span class="thumb_border"></span>
    </span>
    </a>
    </li>
    </ul>
    <a class="tx" href="/photo/issue/846241/100#cid=846241&amp;iid=1457345" onclick="nclk(this, 'com.p1art', '', '');"><strong>육성재 "'도깨비'로 연기에 자신감 붙었다" </strong></a>
    </div>
    </div>
    <span class="btn_arr prev"><a href="#" onclick="nclk(this, 'com.p1prev', '', '');"><span class="sp_ico"><span class="blind">이전</span></span></a></span>
    <span class="btn_arr next"><a href="#" onclick="nclk(this, 'com.p1next', '', '');"><span class="sp_ico"><span class="blind">다음</span></span></a></span>
    </div>
    <a class="btn_menu_more" href="/photo/issue/846241/100" onclick="nclk(this, 'com.p1more', '', '');"><span class="sp_ico"></span>더보기</a>
    </div>
    </div>
    <div class="box" id="rightTvRolling998568">
    <div class="slide_area">
    <h4 class="aside_tit">TV 하이라이트</h4>
    <div class="tv_slide">
    <ul class="slide_lst">
    <li>
    <a alt="복면가왕" class="name" href="/tvBrand/2404757" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="복면가왕" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2015/04/17/195923404_%25BA%25B9%25B8%25E9%25B0%25A1%25BF%25D5_1136_460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="복면가왕" class="name" href="/tvBrand/2404757" onclick="nclk(this, 'com.btxt', '', '');">mbc<span class="sp_ico"></span>복면가왕</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="슈퍼맨이 돌아왔다" class="name" href="/tvBrand/675566" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="슈퍼맨이 돌아왔다" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2016/04/15/133613035_%25BD%25B4%25C6%25DB%25B8%25C7%25C0%25CC%25B5%25B9%25BE%25C6%25BF%25D4%25B4%25D9_1136_460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="슈퍼맨이 돌아왔다" class="name" href="/tvBrand/675566" onclick="nclk(this, 'com.btxt', '', '');">kbs<span class="sp_ico"></span>슈퍼맨이 돌아왔다</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="판타스틱 듀오 2" class="name" href="/tvBrand/5413933" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="판타스틱 듀오 2" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2017/04/14/160507509_%25C6%25C7%25C5%25B8%25BD%25BA%25C6%25BD%25B5%25E0%25BF%25C02_1136x460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="판타스틱 듀오 2" class="name" href="/tvBrand/5413933" onclick="nclk(this, 'com.btxt', '', '');">sbs<span class="sp_ico"></span>판타스틱 듀오 2</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="1박2일 시즌3" class="name" href="/tvBrand/675591" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="1박2일 시즌3" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2016/08/07/112530996_1%25B9%25DA2%25C0%25CF_1136x460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="1박2일 시즌3" class="name" href="/tvBrand/675591" onclick="nclk(this, 'com.btxt', '', '');">kbs<span class="sp_ico"></span>1박2일 시즌3</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="SBS 인기가요" class="name" href="/tvBrand/658960" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="SBS 인기가요" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2017/02/08/171603849_%25C0%25CE%25B1%25E2%25B0%25A1%25BF%25E4_1136x460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="SBS 인기가요" class="name" href="/tvBrand/658960" onclick="nclk(this, 'com.btxt', '', '');">sbs<span class="sp_ico"></span>SBS 인기가요</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="런닝맨" class="name" href="/tvBrand/674981" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="런닝맨" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2017/05/22/165733079_%25B7%25B1%25B4%25D7%25B8%25C7_1136x460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="런닝맨" class="name" href="/tvBrand/674981" onclick="nclk(this, 'com.btxt', '', '');">sbs<span class="sp_ico"></span>런닝맨</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="미운 우리 새끼" class="name" href="/tvBrand/3612946" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="미운 우리 새끼" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2017/01/06/141414419_%25B9%25CC%25BF%25EE%25BF%25EC%25B8%25AE%25BB%25F5%25B3%25A2_1136x460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="미운 우리 새끼" class="name" href="/tvBrand/3612946" onclick="nclk(this, 'com.btxt', '', '');">sbs<span class="sp_ico"></span>미운 우리 새끼</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="무한도전" class="name" href="/tvBrand/659910" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="무한도전" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2015/09/16/174245564_%25B9%25AB%25C7%25D1%25B5%25B5%25C0%25FC_1136_460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="무한도전" class="name" href="/tvBrand/659910" onclick="nclk(this, 'com.btxt', '', '');">mbc<span class="sp_ico"></span>무한도전</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="불후의 명곡" class="name" href="/tvBrand/670142" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="불후의 명곡" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2016/05/20/190652867_%25BA%25D2%25C8%25C4%25C0%25C7%25B8%25ED%25B0%25EE_1136x460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="불후의 명곡" class="name" href="/tvBrand/670142" onclick="nclk(this, 'com.btxt', '', '');">kbs<span class="sp_ico"></span>불후의 명곡</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="아는 형님" class="name" href="/tvBrand/3052901" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="아는 형님" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2016/07/22/173108758_%25BE%25C6%25B4%25C2%25C7%25FC%25B4%25D4_1136_460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="아는 형님" class="name" href="/tvBrand/3052901" onclick="nclk(this, 'com.btxt', '', '');">jtbc<span class="sp_ico"></span>아는 형님</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="시카고 타자기" class="name" href="/tvBrand/5236369" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="시카고 타자기" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2017/04/05/153529242_%25BD%25C3%25C4%25AB%25B0%25ED%25C5%25B8%25C0%25DA%25B1%25E2_1136x460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="시카고 타자기" class="name" href="/tvBrand/5236369" onclick="nclk(this, 'com.btxt', '', '');">tvN<span class="sp_ico"></span>시카고 타자기</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="맨투맨" class="name" href="/tvBrand/3471230" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="맨투맨" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2017/04/18/143540294_%25B8%25C7%25C5%25F5%25B8%25C7_1136x460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="맨투맨" class="name" href="/tvBrand/3471230" onclick="nclk(this, 'com.btxt', '', '');">jtbc<span class="sp_ico"></span>맨투맨</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="마이 리틀 텔레비전" class="name" href="/tvBrand/2343346" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="마이 리틀 텔레비전" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2015/05/19/164131934_%25B8%25B6%25C0%25CC%25B8%25AE%25C6%25B2%25C5%25DA%25B7%25B9%25BA%25F1%25C0%25FC_1136_460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="마이 리틀 텔레비전" class="name" href="/tvBrand/2343346" onclick="nclk(this, 'com.btxt', '', '');">mbc<span class="sp_ico"></span>마이 리틀 텔레비전</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="언니들의 슬램덩크 2" class="name" href="/tvBrand/4520676" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="언니들의 슬램덩크 2" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2017/02/17/104217429_%25BE%25F0%25B4%25CF%25B5%25E9%25C0%25C7-%25BD%25BD%25B7%25A5%25B5%25A2%25C5%25A92_1136_460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="언니들의 슬램덩크 2" class="name" href="/tvBrand/4520676" onclick="nclk(this, 'com.btxt', '', '');">kbs<span class="sp_ico"></span>언니들의 슬램덩크 2</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="프로듀스 101 시즌2" class="name" href="/tvBrand/5019505" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="프로듀스 101 시즌2" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2017/04/05/153348714_%25C7%25C1%25B7%25CE%25B5%25E0%25BD%25BA101-%25BD%25C3%25C1%25F02_1136x460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="프로듀스 101 시즌2" class="name" href="/tvBrand/5019505" onclick="nclk(this, 'com.btxt', '', '');">mnet<span class="sp_ico"></span>프로듀스 101 시즌2</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="나 혼자 산다" class="name" href="/tvBrand/673019" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="나 혼자 산다" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2015/03/13/175736348_%25B3%25AA%25C8%25A5%25C0%25DA%25BB%25EA%25B4%25D9_1136_460.png?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="나 혼자 산다" class="name" href="/tvBrand/673019" onclick="nclk(this, 'com.btxt', '', '');">mbc<span class="sp_ico"></span>나 혼자 산다</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="정글의 법칙 와일드 뉴질랜드" class="name" href="/tvBrand/5868180" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="정글의 법칙 와일드 뉴질랜드" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2017/05/18/144607038_%25C1%25A4%25B1%25DB%25C0%25C7%25B9%25FD%25C4%25A2_1136x460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="정글의 법칙 와일드 뉴질랜드" class="name" href="/tvBrand/5868180" onclick="nclk(this, 'com.btxt', '', '');">sbs<span class="sp_ico"></span>정글의 법칙 와일드 뉴질랜드</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="백종원의 3대 천왕" class="name" href="/tvBrand/2884790" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="백종원의 3대 천왕" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2017/04/21/142908098_%25B9%25E9%25C1%25BE%25BF%25F8%25C0%25C73%25B4%25EB%25C3%25B5%25BF%25D5_1136x460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="백종원의 3대 천왕" class="name" href="/tvBrand/2884790" onclick="nclk(this, 'com.btxt', '', '');">sbs<span class="sp_ico"></span>백종원의 3대 천왕</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="공조7" class="name" href="/tvBrand/5404600" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="공조7" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2017/03/24/164751584_%25B0%25F8%25C1%25B67_1136x460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="공조7" class="name" href="/tvBrand/5404600" onclick="nclk(this, 'com.btxt', '', '');">tvN<span class="sp_ico"></span>공조7</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="군주 - 가면의 주인" class="name" href="/tvBrand/4571945" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="군주 - 가면의 주인" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2017/05/10/150948631_%25B1%25BA%25C1%25D6_1136x460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="군주 - 가면의 주인" class="name" href="/tvBrand/4571945" onclick="nclk(this, 'com.btxt', '', '');">mbc<span class="sp_ico"></span>군주 - 가면의 주인</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="수상한 파트너" class="name" href="/tvBrand/5484393" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="수상한 파트너" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2017/05/08/102208454_%25BC%25F6%25BB%25F3%25C7%25D1%25C6%25C4%25C6%25AE%25B3%25CA_1136x460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="수상한 파트너" class="name" href="/tvBrand/5484393" onclick="nclk(this, 'com.btxt', '', '');">sbs<span class="sp_ico"></span>수상한 파트너</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="추리의 여왕" class="name" href="/tvBrand/5342043" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="추리의 여왕" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2017/04/05/153220684_%25C3%25DF%25B8%25AE%25C0%25C7%25BF%25A9%25BF%25D5_1136x460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="추리의 여왕" class="name" href="/tvBrand/5342043" onclick="nclk(this, 'com.btxt', '', '');">kbs<span class="sp_ico"></span>추리의 여왕</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="해피투게더3" class="name" href="/tvBrand/661022" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="해피투게더3" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2016/04/19/162615573_%25C7%25D8%25C7%25C7%25C5%25F5%25B0%25D4%25B4%25F5_1136_460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="해피투게더3" class="name" href="/tvBrand/661022" onclick="nclk(this, 'com.btxt', '', '');">kbs<span class="sp_ico"></span>해피투게더3</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="너의 목소리가 보여 4" class="name" href="/tvBrand/5307439" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="너의 목소리가 보여 4" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2017/03/02/151707322_%25B3%25CA%25B8%25F1%25BA%25B84_1136x460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="너의 목소리가 보여 4" class="name" href="/tvBrand/5307439" onclick="nclk(this, 'com.btxt', '', '');">mnet<span class="sp_ico"></span>너의 목소리가 보여 4</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="엠카운트다운" class="name" href="/tvBrand/659252" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="엠카운트다운" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2016/07/19/140034979_%25BF%25A5%25C4%25AB%25BF%25EE%25C6%25AE%25B4%25D9%25BF%25EE_1136_460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="엠카운트다운" class="name" href="/tvBrand/659252" onclick="nclk(this, 'com.btxt', '', '');">mnet<span class="sp_ico"></span>엠카운트다운</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="한끼줍쇼" class="name" href="/tvBrand/4079732" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="한끼줍쇼" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2017/02/20/191019149_%25C7%25D1%25B3%25A2%25C1%25DD%25BC%25EE_1136x460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="한끼줍쇼" class="name" href="/tvBrand/4079732" onclick="nclk(this, 'com.btxt', '', '');">jtbc<span class="sp_ico"></span>한끼줍쇼</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="라디오스타" class="name" href="/tvBrand/672403" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="라디오스타" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2015/03/13/175340866_%25B6%25F3%25B5%25F0%25BF%25C0%25BD%25BA%25C5%25B8_1136_460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="라디오스타" class="name" href="/tvBrand/672403" onclick="nclk(this, 'com.btxt', '', '');">mbc<span class="sp_ico"></span>라디오스타</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="현장토크쇼 TAXI" class="name" href="/tvBrand/661396" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="현장토크쇼 TAXI" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2015/03/13/174921505_%25C5%25C3%25BD%25C3_1136_460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="현장토크쇼 TAXI" class="name" href="/tvBrand/661396" onclick="nclk(this, 'com.btxt', '', '');">tvN<span class="sp_ico"></span>현장토크쇼 TAXI</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="웃찾사" class="name" href="/tvBrand/673280" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="웃찾사" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2015/03/27/235048841_%25BF%25F4%25C3%25A3%25BB%25E7_1136_460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="웃찾사" class="name" href="/tvBrand/673280" onclick="nclk(this, 'com.btxt', '', '');">sbs<span class="sp_ico"></span>웃찾사</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="쌈, 마이웨이" class="name" href="/tvBrand/5398994" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="쌈, 마이웨이" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2017/05/18/143226618_%25BD%25D3%25B8%25B6%25C0%25CC%25BF%25FE%25C0%25CC_1136x460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="쌈, 마이웨이" class="name" href="/tvBrand/5398994" onclick="nclk(this, 'com.btxt', '', '');">kbs<span class="sp_ico"></span>쌈, 마이웨이</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="파수꾼" class="name" href="/tvBrand/5385476" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="파수꾼" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2017/05/22/165848833_%25C6%25C4%25BC%25F6%25B2%25DB_1136x460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="파수꾼" class="name" href="/tvBrand/5385476" onclick="nclk(this, 'com.btxt', '', '');">mbc<span class="sp_ico"></span>파수꾼</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="써클 : 이어진 두 세계" class="name" href="/tvBrand/5396316" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="써클 : 이어진 두 세계" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2017/05/18/143419488_%25BD%25E1%25C5%25AC_1136x460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="써클 : 이어진 두 세계" class="name" href="/tvBrand/5396316" onclick="nclk(this, 'com.btxt', '', '');">tvN<span class="sp_ico"></span>써클 : 이어진 두 세계</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="안녕하세요" class="name" href="/tvBrand/665142" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="안녕하세요" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2015/03/13/174831179_%25BE%25C8%25B3%25E7%25C7%25CF%25BC%25BC%25BF%25E4_1136_460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="안녕하세요" class="name" href="/tvBrand/665142" onclick="nclk(this, 'com.btxt', '', '');">kbs<span class="sp_ico"></span>안녕하세요</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="냉장고를 부탁해" class="name" href="/tvBrand/2080759" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="냉장고를 부탁해" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2016/02/12/102428120_%25B3%25C3%25C0%25E5%25B0%25ED%25B8%25A6-%25BA%25CE%25C5%25B9%25C7%25D8_1136_460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="냉장고를 부탁해" class="name" href="/tvBrand/2080759" onclick="nclk(this, 'com.btxt', '', '');">jtbc<span class="sp_ico"></span>냉장고를 부탁해</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="비정상회담" class="name" href="/tvBrand/1972622" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="비정상회담" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2016/12/14/214519508_%25BA%25F1%25C1%25A4%25BB%25F3%25C8%25B8%25B4%25E3_1136x460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="비정상회담" class="name" href="/tvBrand/1972622" onclick="nclk(this, 'com.btxt', '', '');">jtbc<span class="sp_ico"></span>비정상회담</a>
    </p>
    </li>
    <li style="display:none;">
    <a alt="윤식당" class="name" href="/tvBrand/5472997" onclick="nclk(this, 'com.bimg', '', '');">
    <img alt="윤식당" onerror="imageErrorByParent1(this)" src="http://mimgnews1.naver.net/image/upload/component/2017/03/24/112416533_%25C0%25B1%25BD%25C4%25B4%25E7_1136x460.jpg?type=hc300_180_q90"/>
    <span class="thumb_border"></span>
    </a>
    <p class="txt">
    <a alt="윤식당" class="name" href="/tvBrand/5472997" onclick="nclk(this, 'com.btxt', '', '');">tvN<span class="sp_ico"></span>윤식당</a>
    </p>
    </li>
    </ul>
    <span class="btn_arr prev"><a alt="이전" href="#" onclick="nclk(this, 'com.bprev', '', '');"><span class="sp_ico"><span class="blind">이전</span></span></a></span>
    <span class="btn_arr next"><a alt="다음" href="#" onclick="nclk(this, 'com.bnext', '', '');"><span class="sp_ico"><span class="blind">다음</span></span></a></span>
    </div>
    </div>
    </div>
    </div>
    <!-- //본문 우측영역 -->
    </div>
    <div id="footer">
    <ul>
    <li class="first"><a alt="이용약관" href="http://www.naver.com/rules/service.html" onclick="nclk(this, 'fot.agreement', '', '');">이용약관</a></li>
    <li><a alt="서비스 운영 원칙" href="http://news.naver.com/main/ombudsman/edit.nhn?mid=omb" onclick="nclk(this, 'fot.editor', '', '');">서비스 운영 원칙</a></li>
    <li><strong><a alt="개인정보취급방침" href="http://www.naver.com/rules/privacy.html" onclick="nclk(this, 'fot.privacy', '', '');">개인정보 처리방침</a></strong></li>
    <li><a alt="책임의 한계와 법적고지" href="http://www.naver.com/rules/disclaimer.html" onclick="nclk(this, 'fot.disclaimer', '', '');">책임의 한계와 법적고지</a></li>
    <li><a alt="TV연예 고객센터" href="https://help.naver.com/support/service/main.nhn?serviceNo=997" onclick="nclk(this, 'fot.help', '', '');" target="blank">TV연예 고객센터</a></li>
    <li><a alt="TV연예 제휴제안" href="https://www.navercorp.com/ko/company/proposalGuide.nhn" onclick="nclk(this, 'fot.contact', '', '');" target="blank">TV연예 제휴제안</a></li>
    </ul>
    <p class="copyright">본 콘텐츠의 저작권은 제공처 또는 네이버에 있으며 이를 무단 이용하는 경우 저작권법 등에 따라 법적책임을 질 수 있습니다.</p>
    <address class="address_cp">Copyright © <a href="http://www.seoul.co.kr/" onclick="nclk(this, 'fot.cp', '', '');" target="blank">서울신문</a> All Rights Reserved.</address>
    <address class="address_nhn">
    <a alt="NAVER" class="logo" href="http://www.navercorp.com/" onclick="nclk(this, 'fot.naver', '', '');" target="_blank"><span class="blind">NAVER</span></a>
    <em>Copyright ©</em>
    <a alt="NAVER Corp." href="http://www.navercorp.com/" onclick="nclk(this, 'fot.naver', '', '');" target="_blank">NAVER Corp.</a>
    <span>All Rights Reserved.</span>
    </address>
    </div>
    <div class="m_version" id="mobileLinkWrp" style="display:none">
    <a alt="모바일 버전으로 보기" href="http://m.entertain.naver.com/read?oid=081&amp;aid=0002824625" onclick="nclk(this, 'fot.mobile', '', '');">
    <span class="tx">모바일 버전으로 보기</span><span class="icon"></span>
    </a>
    </div>
    </div>
    <script src="http://static.entertain.naver.net/resources/20170522_165620/js/generated/naver.enter.common.js" type="text/javascript"></script>
    <script src="http://static.entertain.naver.net/resources/20170522_165620/js/generated/naver.enter.article.read.js" type="text/javascript"></script>
    <script src="http://static.entertain.naver.net/resources/20170522_165620/js/generated/naver.enter.article.read.analysys.js" type="text/javascript"></script>
    <script src="http://static.entertain.naver.net/resources/20170522_165620/js/generated/naver.enter.right.js" type="text/javascript"></script>
    <script charset="utf-8" type="text/javascript">
    jindo.$Fn(function() {
    	 
    
    	var oDate;
    	try {
    		oDate = new jindo.$Date(Date.now());
    	} catch (error){
    				
    		oDate = new jindo.$Date(new Date());
    	}
    	var currentTimeStamp = oDate.format("YmdHis");
    	
    	enter.read.init({
    		oLikeItListData : {
    			sDomain : "http://news.like.naver.com"
    		},
    		oLikeItData : {
    			serviceId : "ENTERTAIN" // 좋아요 데이터 제공 서비스ID
    			, displayId : "ENTERTAIN" // 좋아요 노출 서비스ID (optional)
    			, contentId : "ne_081_0002824625"
    			, domain : "http://news.like.naver.com"
    			, lang : "ko" // 좋아요 언어 설정 (optional) : 입력되지 않으면 브라우져의 언어설정에 따른 언어로 좋아요가 노출됩니다.
    			//, viewType : "recommend" //좋아요 뷰 유형 설정 (optional) : 입력되지 않으면 기본 좋아요 유형으로 노출됩니다.
    			, countCallbackYn : "N"
    		},
    		oSpiData : {
    			domain : 'http://spi.naver.net'
    		},
    		oReadingData : {
    			sContentsId : 'articeBody',
    			oid : '081',
    			aid : '0002824625',
    			sid : '106',
    			articleType : '1',
    			gdid : '880000D1_000000000000000002824625',
    			timestamp : currentTimeStamp
    		}
    	});
    		
    }, this).attach(window, 'load');
    </script>
    <script type="text/javascript">
    var htParameter = {
    	evKey : "entertain",
    	dimmed : "fixed",
    	serviceName : "TV연예",
    	sourceName : "서울신문",
    	url : "http://entertain.naver.com/read?oid=081&aid=0002824625",
    	title : "‘아는형님’ 오현경, “강호동이 이상형..고백했으면 사귀었을 것”",
    	me : {
    		display : "off"
    	},
    	mail : {
    		srvId : "news",
    		srvUrl : "http://news.naver.com/main/tool/mailContents.nhn?oid=081&aid=0002824625",
    	},
    	blog : {
    		blogId : "naver",
    		sourceType : "3",
    	},
    	cafe : {
    		sourceType : "3",
    	},
    	facebook : {
    		url : "http://entertain.naver.com/read?oid=081&aid=0002824625" + "&amp;fbrefresh=" + jindo.$Date().format("YmdHi"),
    	}
    };
    
    function onSubscribe(elTarget) {
    	return htParameter;
    }
    </script>
    <script type="text/javascript">
    jindo.$Fn(function() {
    	enter.read.section.guide.init();
    }, this).attach(window, 'load');
    </script>
    <script type="text/javascript">
    var oCircularTvCastRolling;
    jindo.$Fn(function() {	
    	if (jindo.$$.getSingle("#tvcast_rolling")) {	
    		oCircularTvCastRolling = new jindo.CircularRolling(jindo.$("tvcast_rolling"), {
    			sClassPrefix : 'rolling-'
    			, nDuration : 300
    		});	
    	} 
    	
    	var readCommentTemplateId;
    	var readCssId;
    	try{
    		var thisPageUrl = location.href;
    		if(thisPageUrl.indexOf('/starcast/')> 0 ) {
    			readCommentTemplateId = 'view_starcast'; 
    			readCssId = 'enter'; 
    		} else {
    			readCommentTemplateId = 'view_ent';
    			readCssId = 'enter'; 
    		}
    	} catch(err){
    		readCommentTemplateId = 'view_ent';
    		readCssId = 'enter'; 
    	}	
    	
    	
    	enter.common.comment.init({
    		sDomain : 'http://static.cbox.naver.net',
    		sApiDomain : 'https://apis.naver.com/commentBox/cbox5',
    		sTicket : 'news',
    		sObjectId : 'news081,0002824625',
    		nPageSize : 10,
    	 	nReplyPageSize : 20,
    	 	sCssId : readCssId,
    		sTemplateId : readCommentTemplateId,
    		sCountType : 'comment',
    	 	sAuthType : 'both',
    		aFormation :  ['count', 'graph', 'write', 'notice', 'list', 'view'], 
    		sHelp : 'up',
    		sLikeItId : "ne_081_0002824625",  // 컨텐츠 ID,
    		htEventHandler : {
    		    	loadList : function(oEvent) {
    		    		var oJson = oEvent.oJson;
    					var welCommentCount = jindo.$Element('cmt_count');
    					
    					var sTempHtml = "<span class=\"bar\"></span><span class=\"sp_ico\"><span class=\"blind\">댓글</span></span>";
    			        if (oJson.success) {
    			            var commentCnt = oJson.result.count.comment;
    			            sTempHtml += jsutility.changeNumberFormat(commentCnt);
    			        } else {
    			        	sTempHtml += "0";
    			        }
    			        
    					welCommentCount.html(sTempHtml);
    			    },
    			    loadCreate : function(oEvent) {
    			    	var oJson = oEvent.oJson;
    				        if (oJson.success) {
    				        	commentCountUpdate(oJson.result.count.comment);
    				        }
    			   	 },
    			   	afterRemove : function(oEvent) {
    			    	var oJson = oEvent.oJson;
    				        if (oJson.success) {
    				        	commentCountUpdate(oJson.result.count.comment);
    
    				        }
    			   	 }
    		},
    		vViewAllHandler : function(){
    	    	window.location.href = '/comment/list?oid=081&aid=0002824625';
    	    },
    		vWriterLinkHandler : function(oParam) {
    		if (oParam.profileType == 'naver') {
    	    		window.location.href = "javascript:popupOpen('/comment/usercomment');";
    		} else if (oParam.profileType == 'facebook') {
    			window.location.href = "http://www.facebook.com/" + oParam.profileUserId;
    		} else if (oParam.profileType == 'twitter') {
    			window.location.href = "https://twitter.com/"+oParam.nickname;  
    		} 
       	}, htErrorHandler : { 
    	    	'5010' : '댓글/답글은 60초 내에 한 개만 등록하실 수 있습니다.' 
    	    	, '5006' : '자신의 글에 공감 할 수 없습니다.' 
    	    	, '5008' : '자신의 글에 비공감 할 수 없습니다.' 
    	    	, '4004' : '해당되는 댓글이 없습니다.' 
    	    	, '5003' : '정상적으로 반영되지 않았습니다. 화면 새로고침 후 다시 시도해 주세요.' 
    	    }, htMessage : {
    		    alert : { min_length : '내용을 최소 {0}자 이상 입력해주세요.' 
    		    	,max_length : '{0}자까지 입력할 수 있습니다.' 
    		    	, login_require : '입력창 상단의 서비스별 아이콘을 선택하여 로그인해 주세요.' }
    		    , template : {
    		    write_placeholder : '댓글을 입력해주세요'
    		    , write_long_placeholder : '저작권 등 다른 사람의 권리를 침해하거나 명예를 훼손하는 게시물은 이용약관 및 관련 법률에 의해 제재를 받을 수 있습니다. 건전한 토론문화와 양질의 댓글 문화를 위해, 타인에게 불쾌감을 주는 욕설 또는 특정 계층/민족, 종교 등을 비하하는 단어들은 표시가 제한됩니다.'
    		    //모바일일 경우
    		    , help_titles : '소셜댓글안내'
    		    , help_contents : '소셜댓글은 네이버 아이디 뿐만 아니라 가입한 페이스북, 트위터의 아이디로도 로그인 할 수 있으며, 작성한 댓글은 로그인한 SNS에도 등록되는 새로운 소셜 댓글 서비스 입니다. 계정을 선택하시면 로그인/계정인증을 통해 댓글을 남기실 수 있습니다. ' 
    		    //피씨일경우
    		    , help_titles : function()
    		    { return ["소셜댓글안내","댓글노출정책"]; }
    		    , help_contents : function()
    		    { return ["소셜댓글은 네이버 아이디 뿐만 아니라 가입한 페이스북, 트위터의 아이디로도 로그인 할 수 있으며, 작성한 댓글은 로그인한 SNS에도 등록되는 새로운 소셜 댓글 서비스 입니다. 계정을 선택하시면 로그인/계정인증을 통해 댓글을 남기실 수 있습니다." ,"호감순은 호감수(공감수에서 일정비율의 비공감수를 뺀 수치)가 많은 댓글입니다. 비정상적인 방법으로 공감수가 증가하거나 타인에게 불쾌감을 주는 경우 제외될 수 있습니다."]; }
    		    }, stats : {
                    title : '누가 댓글을 썼을까요?',
                    view : '통계보기',
                    fold : '접기'
             	} 
    	    } 
    	});
    
    }, this).attach(window, 'load');
    
    /* 댓글이 0 일때 전체 댓글 보기 구문 가리기 */
    jindo.$Fn(function() {
    	try{		
    		 if (jindo.$$.getSingle('.u_cbox_comment_none')) {
    			 	var commentArea = jindo.$Element(jindo.$$.getSingle('.u_cbox_btn_view_comment'));
    			 	commentArea.hide();
    		 }
    	} catch(e){
    	}
    }).delay(1);
    
    </script>
    <script type="text/javascript">
    jindo.$Fn(function() {
    	new TabManager('ranking_news');
    }, this).attach(window, 'load');
    </script><script type="text/javascript">
    jindo.$Fn(function() {
    	new SlideWithDotManager('rightBannerRolling1017109',{
    		sSlideClassName : 'slide_lst',
    		sPrevClassName : 'btn_prev',
    		sNextClassName : 'btn_next'
    	});
    }, this).attach(window, 'load');
    </script><script type="text/javascript">
    jindo.$Fn(function() {
    	new TabManager('main_news');
    }, this).attach(window, 'load');
    </script><script type="text/javascript">
    jindo.$Fn(function() {
    	new SlideWithDotManager('rightBannerRolling1013200',{
    		sSlideClassName : 'slide_lst',
    		sPrevClassName : 'btn_prev',
    		sNextClassName : 'btn_next'
    	});
    }, this).attach(window, 'load');
    </script><script type="text/javascript">
    jindo.$Fn(function() {
    	try{
    		new SlideManager('rightHotPhotoSlide846241');	
    	} catch(e){
    		console.error(e);
    	}//catch
    }, this).attach(window, 'load');
    </script><script type="text/javascript">
    jindo.$Fn(function() {
    	new SlideManager('rightTvRolling998568');
    }, this).attach(window, 'load');
    </script>
    <script charset="utf-8" type="text/javascript">
    
    try{
    	function analysysExe(isDevelopEnv){		
    		nil.init('naver_news', '081');
    		if (isDevelopEnv)
    		{ 			
    			nil.setDevMode(true); 
    		}
    		
    		nil.add({
    		    'uri': 'http://news.naver.com/main/read.nhn?oid=081&aid=0002824625',
    		    'dimension_1': '%EC%97%B0%EC%98%88' // 연예
    		 });
    		
    		//cv 측정
    		nil.send('cv', 'post_view');
    		
    		//체류시간 측정
    		window.onunload = function() {
    		  nil.send('leave');
    		}		
    	}
    
    } catch (e) {
    }
    
    </script>
    <script charset="utf-8" type="text/javascript">
    	analysysExe(false);
    	</script>
    <script type="text/javascript">
    (function(){
    	var welMobileLinkWrp = jindo.$Element("mobileLinkWrp");
    	if(!welMobileLinkWrp) return;
    	if (jindo.$Agent().navigator().mobile) {
    		welMobileLinkWrp.show();
    	}
    	welMobileLinkWrp.attach("click", function() {
    		jindo.$Cookie().set("enter_pc","false", 0, "entertain.naver.com");
    	});
    })();
    </script>
    <script type="text/javascript">
    if(true) {	
    	lcs_do_gdid('880000D1_000000000000000002824625');
    }else if(typeof lcs_do !== "undefined"){
    	lcs_do();
    }
    try {
    	var e = new Image();
    	e.src = "http://l.msdl.naver.com/ent?oid=081&aid=0002824625&onepath="+ location.href.split("?")[0].split("/")[3];
    } catch (e) {
    }
    </script>
    </body>
    </html>
    

* **네이버뉴스에서 div 태그에 있는 articeBody를 가져오기**
* **articeBody는 본문 내용이며, 이 html태그는 chrome에서 F12를 눌러 확인가능**
* **class 이름은 바뀔 수 있음을 유의. 예를 들면 세계일보는 articeBody, 조선일보는 articleBodyContents를 사용하기도 함**


```python
article_body = soup2.find('div', id='articeBody')
print(article_body)
```

    <div class="article_body font1 size3" id="articeBody">
    <!-- [D] 본문 폰트 설정 class 안내
    							나눔고딕 : font1
    							맑은고딕 : font2
    							돋움 : font3
    							바탕 : font4
    							폰트 사이즈 1 : size1
    							폰트 사이즈 2 : size2
    							폰트 사이즈 3 : size3
    							폰트 사이즈 4 : size4
    							폰트 사이즈 5 : size5
    						-->
    						
    							
    						
    						[서울신문 En]<br/><span class="end_photo_org"> <img id="img1" onerror='javascript:this.src="http://static.news.naver.net/image/news/2009/press/empty.png";javascript:this.height="0px";javascript:imageErrorDetector(1, "http://mimgnews1.naver.net/image/081/2017/05/28/0002824625_001_20170528143116159.jpg");' src="http://mimgnews1.naver.net/image/081/2017/05/28/0002824625_001_20170528143116159.jpg?type=w540"/>
    <em class="img_desc">‘아는형님’ 오현경</em></span>배우 오현경이 “강호동이 이상형이다”고 고백했다.<!-- MobileAdNew center --><br/><br/>오현경은 27일 JTBC ‘아는 형님’에 출연해 이같이 말했다. 강호동과 25년 지기 친구인 오현경은 “나보고 반한 적 없어?”라고 ‘기습’ 질문했다.<br/><br/>이에 강호동은 고개를 숙이고 손수건으로 이마 땀을 닦는 등 부끄러움을 감추지 못했다. 이어 오현경은 “만약 호동이가 대시했다면 사귀었을 것”이라고 말했다.<br/><br/>또한 오현경은 “사실 내 이상형은 강호동이다”고 말해 출연진을 당황하게 만들었다. 이어 그는 “지금 장난치는 거야. 너 결혼했는데 그러면 안된다”고 덧붙여 웃음을 자아냈다.<br/><br/>한편 이날 강호동은 “오현경과 친구로 지낸지는 25년, 실제로 본 지는 28년 된다”며 오현경과의 우정을 과시했다.<br/><br/>그는 “오현경이 (1989년) 미스코리아가 됐을 때 나도 백두장사가 됐다. 모 언론 인터뷰를 갔는데 그 때 엘리베이터에서 만났다”고 회상했다.<br/><br/>사진 = 서울신문DB<!-- MobileAdNew center --><br/><br/>연예팀 seoulen@seoul.co.kr<br/><br/>▶ [<a href="http://en.seoul.co.kr">서울EN 바로가기</a>] [<a href="https://www.facebook.com/seoultv/">페이스북</a>] <br/>▶ [<a href="http://en.seoul.co.kr/news/newsList.php?section=starinterN">스타 화보 보기</a>]<br/><br/><br/><br/>ⓒ 서울신문(<a href="http://www.seoul.co.kr">www.seoul.co.kr</a>), 무단전재 및 재배포금지
    					</div>
    

* **텍스트 부분만 가져오기**


```python
dirt_text = article_body.find_all(text=True)
print(dirt_text)
```

    ['\n', ' [D] 본문 폰트 설정 class 안내\n\t\t\t\t\t\t\t나눔고딕 : font1\n\t\t\t\t\t\t\t맑은고딕 : font2\n\t\t\t\t\t\t\t돋움 : font3\n\t\t\t\t\t\t\t바탕 : font4\n\t\t\t\t\t\t\t폰트 사이즈 1 : size1\n\t\t\t\t\t\t\t폰트 사이즈 2 : size2\n\t\t\t\t\t\t\t폰트 사이즈 3 : size3\n\t\t\t\t\t\t\t폰트 사이즈 4 : size4\n\t\t\t\t\t\t\t폰트 사이즈 5 : size5\n\t\t\t\t\t\t', '\n\t\t\t\t\t\t\n\t\t\t\t\t\t\t\n\t\t\t\t\t\t\n\t\t\t\t\t\t[서울신문 En]', ' ', '\n', '‘아는형님’ 오현경', '배우 오현경이 “강호동이 이상형이다”고 고백했다.', ' MobileAdNew center ', '오현경은 27일 JTBC ‘아는 형님’에 출연해 이같이 말했다. 강호동과 25년 지기 친구인 오현경은 “나보고 반한 적 없어?”라고 ‘기습’ 질문했다.', '이에 강호동은 고개를 숙이고 손수건으로 이마 땀을 닦는 등 부끄러움을 감추지 못했다. 이어 오현경은 “만약 호동이가 대시했다면 사귀었을 것”이라고 말했다.', '또한 오현경은 “사실 내 이상형은 강호동이다”고 말해 출연진을 당황하게 만들었다. 이어 그는 “지금 장난치는 거야. 너 결혼했는데 그러면 안된다”고 덧붙여 웃음을 자아냈다.', '한편 이날 강호동은 “오현경과 친구로 지낸지는 25년, 실제로 본 지는 28년 된다”며 오현경과의 우정을 과시했다.', '그는 “오현경이 (1989년) 미스코리아가 됐을 때 나도 백두장사가 됐다. 모 언론 인터뷰를 갔는데 그 때 엘리베이터에서 만났다”고 회상했다.', '사진 = 서울신문DB', ' MobileAdNew center ', '연예팀 seoulen@seoul.co.kr', '▶ [', '서울EN 바로가기', '] [', '페이스북', '] ', '▶ [', '스타 화보 보기', ']', 'ⓒ 서울신문(', 'www.seoul.co.kr', '), 무단전재 및 재배포금지\n\t\t\t\t\t']
    

* **html 태그 없애는 정규표현식이었던듯..?**


```python
cleaned_text = re.sub('[a-zA-Z]', '', str(dirt_text))
print(cleaned_text)
```

    ['\', ' [] 본문 폰트 설정  안내\\\\\\\\나눔고딕 : 1\\\\\\\\맑은고딕 : 2\\\\\\\\돋움 : 3\\\\\\\\바탕 : 4\\\\\\\\폰트 사이즈 1 : 1\\\\\\\\폰트 사이즈 2 : 2\\\\\\\\폰트 사이즈 3 : 3\\\\\\\\폰트 사이즈 4 : 4\\\\\\\\폰트 사이즈 5 : 5\\\\\\\', '\\\\\\\\\\\\\\\\\\\\\\\\\\\\\[서울신문 ]', ' ', '\', '‘아는형님’ 오현경', '배우 오현경이 “강호동이 이상형이다”고 고백했다.', '   ', '오현경은 27일  ‘아는 형님’에 출연해 이같이 말했다. 강호동과 25년 지기 친구인 오현경은 “나보고 반한 적 없어?”라고 ‘기습’ 질문했다.', '이에 강호동은 고개를 숙이고 손수건으로 이마 땀을 닦는 등 부끄러움을 감추지 못했다. 이어 오현경은 “만약 호동이가 대시했다면 사귀었을 것”이라고 말했다.', '또한 오현경은 “사실 내 이상형은 강호동이다”고 말해 출연진을 당황하게 만들었다. 이어 그는 “지금 장난치는 거야. 너 결혼했는데 그러면 안된다”고 덧붙여 웃음을 자아냈다.', '한편 이날 강호동은 “오현경과 친구로 지낸지는 25년, 실제로 본 지는 28년 된다”며 오현경과의 우정을 과시했다.', '그는 “오현경이 (1989년) 미스코리아가 됐을 때 나도 백두장사가 됐다. 모 언론 인터뷰를 갔는데 그 때 엘리베이터에서 만났다”고 회상했다.', '사진 = 서울신문', '   ', '연예팀 @..', '▶ [', '서울 바로가기', '] [', '페이스북', '] ', '▶ [', '스타 화보 보기', ']', 'ⓒ 서울신문(', '...', '), 무단전재 및 재배포금지\\\\\\']
    

* **\ 이런 것들 없애주는 정규표현식**


```python
cleaned_text = re.sub('[\{\}\[\]\/?.,;:|\)*~`!^\-_+<>@\#$%&\\\=\(\'\"]',
                              '', cleaned_text)
print(cleaned_text)
```

       본문 폰트 설정  안내나눔고딕  1맑은고딕  2돋움  3바탕  4폰트 사이즈 1  1폰트 사이즈 2  2폰트 사이즈 3  3폰트 사이즈 4  4폰트 사이즈 5  5 서울신문     ‘아는형님’ 오현경 배우 오현경이 “강호동이 이상형이다”고 고백했다     오현경은 27일  ‘아는 형님’에 출연해 이같이 말했다 강호동과 25년 지기 친구인 오현경은 “나보고 반한 적 없어”라고 ‘기습’ 질문했다 이에 강호동은 고개를 숙이고 손수건으로 이마 땀을 닦는 등 부끄러움을 감추지 못했다 이어 오현경은 “만약 호동이가 대시했다면 사귀었을 것”이라고 말했다 또한 오현경은 “사실 내 이상형은 강호동이다”고 말해 출연진을 당황하게 만들었다 이어 그는 “지금 장난치는 거야 너 결혼했는데 그러면 안된다”고 덧붙여 웃음을 자아냈다 한편 이날 강호동은 “오현경과 친구로 지낸지는 25년 실제로 본 지는 28년 된다”며 오현경과의 우정을 과시했다 그는 “오현경이 1989년 미스코리아가 됐을 때 나도 백두장사가 됐다 모 언론 인터뷰를 갔는데 그 때 엘리베이터에서 만났다”고 회상했다 사진  서울신문     연예팀  ▶  서울 바로가기   페이스북   ▶  스타 화보 보기  ⓒ 서울신문   무단전재 및 재배포금지
    

* **앞에 달려 있는 "본문 폰트 설정 안내나눔고딕"등의 글들을 없애주는 정규표현식**


```python
cleaned_text = re.sub('본문 폰트 설정  안내나눔고딕  1맑은고딕  2돋움  3바탕  4폰트 사이즈 1  1폰트 사이즈 2  2폰트 사이즈 3  3폰트 사이즈 4  4폰트 사이즈 5  5',
                      '', cleaned_text)
print(cleaned_text)
```

        서울신문     ‘아는형님’ 오현경 배우 오현경이 “강호동이 이상형이다”고 고백했다     오현경은 27일  ‘아는 형님’에 출연해 이같이 말했다 강호동과 25년 지기 친구인 오현경은 “나보고 반한 적 없어”라고 ‘기습’ 질문했다 이에 강호동은 고개를 숙이고 손수건으로 이마 땀을 닦는 등 부끄러움을 감추지 못했다 이어 오현경은 “만약 호동이가 대시했다면 사귀었을 것”이라고 말했다 또한 오현경은 “사실 내 이상형은 강호동이다”고 말해 출연진을 당황하게 만들었다 이어 그는 “지금 장난치는 거야 너 결혼했는데 그러면 안된다”고 덧붙여 웃음을 자아냈다 한편 이날 강호동은 “오현경과 친구로 지낸지는 25년 실제로 본 지는 28년 된다”며 오현경과의 우정을 과시했다 그는 “오현경이 1989년 미스코리아가 됐을 때 나도 백두장사가 됐다 모 언론 인터뷰를 갔는데 그 때 엘리베이터에서 만났다”고 회상했다 사진  서울신문     연예팀  ▶  서울 바로가기   페이스북   ▶  스타 화보 보기  ⓒ 서울신문   무단전재 및 재배포금지
    

* **기사 제목을 가져오기 위해 p 태그에 있는 end_tit class를 가져오기**
* **텍스트 다듬는 것은 위와 동일**


```python
title = soup2.find('p', attrs={'class':'end_tit'})
print(title)
```

    <p class="end_tit">‘아는형님’ 오현경, “강호동이 이상형..고백했으면 사귀었을 것”</p>
    


```python
title2 = title.find_all(text=True)
print(title2)                
```

    ['‘아는형님’ 오현경, “강호동이 이상형..고백했으면 사귀었을 것”']
    


```python
title2 = re.sub('[a-zA-Z]', '', str(title2))
print(title2)               
```

    ['‘아는형님’ 오현경, “강호동이 이상형..고백했으면 사귀었을 것”']
    


```python
title2 = re.sub('[\{\}\[\]\/?.,;:|\)*~`!^\-_+<>@\#$%&\\\=\(\'\"]','', title2)
print(title2)
```

    ‘아는형님’ 오현경 “강호동이 이상형고백했으면 사귀었을 것”
    

* **기사 정보들 가져오기**
* **신문사, 게시 시간 등**


```python
a = item[0]
```


```python
press = a.find('span', {'class': 'press'}).text
print(press)
```

    서울신문
    


```python
date = a.find('span',{'class':'time'}).text
print(date)
```

    2시간전
    


```python
article_url.append(article)
press_name.append(press)
press_time.append(date)  
press_title.append(title2)
press_text.append(cleaned_text)
```

* **Series로 만든 후 DataFrame으로 합친 결과를 내보내면 끝**


```python
date = Series(press_time)
name = Series(press_name)
press_url = Series(article_url)
title_sr = Series(press_title)
text_sr = Series(press_text)

url_df = pd.concat([title_sr, name, date, text_sr, press_url], axis=1)
url_df.columns = ['TITLE', 'NAME', 'TIME', 'TEXT', 'URL']
```


```python
pd.DataFrame(url_df)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>TITLE</th>
      <th>NAME</th>
      <th>TIME</th>
      <th>TEXT</th>
      <th>URL</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>‘아는형님’ 오현경 “강호동이 이상형고백했으면 사귀었을 것”</td>
      <td>서울신문</td>
      <td>Series([], dtype: float64)</td>
      <td>서울신문     ‘아는형님’ 오현경 배우 오현경이 “강호동이 이상형이다”고 ...</td>
      <td>http://news.naver.com/main/read.nhn?mode=LSD&amp;m...</td>
    </tr>
  </tbody>
</table>
</div>



## 완성!


```python
for i in range(1, total_pages+1):
    url = url_basic + str(i)
    
    source_code_from_URL = urllib.request.urlopen(url)
    soup = BeautifulSoup(source_code_from_URL, "lxml", from_encoding = "utf-8")
    item = soup.find_all('div', 'info')
    
    for a in item:
        try:
            article = a.find('a')['href']
        
            news_url = urllib.request.urlopen(article)
            soup2 = BeautifulSoup(news_url, "lxml", from_encoding = "utf-8")
            article_body = soup2.find('div', id='articleBodyContents')
            dirt_text = article_body.find_all(text=True)
            cleaned_text = re.sub('[a-zA-Z]', '', str(dirt_text))
            cleaned_text = re.sub('[\{\}\[\]\/?.,;:|\)*~`!^\-_+<>@\#$%&\\\=\(\'\"]',
                              '', cleaned_text)
            cleaned_text = re.sub('본문 내용    플레이어     플레이어     오류를 우회하기 위한 함수 추가',
                         '', cleaned_text)

            title = soup2.find('h3', id='articleTitle')
            title2 = title.find_all(text=True)
            title2 = re.sub('[a-zA-Z]', '', str(title2))
            title2 = re.sub('[\{\}\[\]\/?.,;:|\)*~`!^\-_+<>@\#$%&\\\=\(\'\"]',
                              '', title2)
            
        except AttributeError:
            try:
                article = a.find('a')['href']
                news_url = urllib.request.urlopen(article)
                soup2 = BeautifulSoup(news_url, "lxml", from_encoding = "utf-8")
                article_body = soup2.find('div', id='articeBody')
                dirt_text = article_body.find_all(text=True)
                cleaned_text = re.sub('[a-zA-Z]', '', str(dirt_text))
                cleaned_text = re.sub('[\{\}\[\]\/?.,;:|\)*~`!^\-_+<>@\#$%&\\\=\(\'\"]',
                                  '', cleaned_text)
                cleaned_text = re.sub('본문 내용    플레이어     플레이어     오류를 우회하기 위한 함수 추가',
                             '', cleaned_text)

                title = soup2.find('p', attrs={'class':'end_tit'})
                title2 = title.find_all(text=True)
                title2 = re.sub('[a-zA-Z]', '', str(title2))
                title2 = re.sub('[\{\}\[\]\/?.,;:|\)*~`!^\-_+<>@\#$%&\\\=\(\'\"]',
                                  '', title2)
            
            except AttributeError:
                title2 = "Error"
                cleaned_text = "Error"
            
            
        
        
        
        press = a.find('span', {'class': 'press'}).text
        date = a.find('span',{'class':'time'}).text
    
        article_url.append(article)
        press_name.append(press)
        press_time.append(date)
        
        press_title.append(title2)
        press_text.append(cleaned_text)
        
    random_time = random.randint(20, 40) # 이 부분은 네이버뉴스를 크롤링할 때 페이지 넘기는 속도 제어하는 부분
    time.sleep(random_time)
```


```python
i
```




    2




```python
date = Series(press_time)
name = Series(press_name)
press_url = Series(article_url)
title_sr = Series(press_title)
text_sr = Series(press_text)

url_df = pd.concat([title_sr, name, date, text_sr, press_url], axis=1)
url_df.columns = ['TITLE', 'NAME', 'TIME', 'TEXT', 'URL']
```


```python
url_df.to_csv(r"D:\test" + file_name + ".csv")
```


```python
print("==================Finished=================")
```

    ==================Finished=================
    
