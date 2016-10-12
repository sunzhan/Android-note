## 得到Document对象
首先得到一个Document对象：
    
    Document document = Jsoup.connect(html).timeout(15000).get();
    
这里调用Jsoup的connect方法传入一个url地址或者一串HTML字符串，然后设置响应时间，最好长一点，因为有些网站响应比较慢，最后调用get（）。
## 解析Document
这里使用Jsoup的select选择器来解析。

    Element element = document.select("div.articleList").first();
        Elements hrefs = element.select("a[title]");
        for (Element ele : hrefs){
            ListBean listBean = new ListBean();
            String href = ele.attr("href");
            String title = ele.text();
        }

传入div.Class名来查找div块，a[title]查找a标签带有title关键字的语句，返回一个Elements，然后用for循环遍历所需要的内容。

    Element element = document.select("[id='sina_keyword_ad_area2']").first();
                        Elements style = element.select("p");
                        String content = "";
                        for (Element element1 : style) {
                            String text = element1.text();
                            content = content + "\n" + "        " + text;
                        }

或者直接查询div中的id，[id='sina_keyword_ad_area2']，然后查询所有p标签的内容，一般一个p标签就是一段正文。

## 特别的
为了防止被检测出是爬虫，可以加上请求头等信息

    document = Jsoup.connect(html)
                            .userAgent("Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/52.0.2743.116 Safari/537.36")
                            //加上cookie信息
                            .cookie("auth", "token")
                            .header("User-Agent","Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10.4; en-US; rv:1.9.2.2) Gecko/20100316 Firefox/3.6.2")
                            .timeout(30000)
                            .get();
                            

        Connection connect = Jsoup.connect(html);
        //                Map<String, String> header = new HashMap<String, String>();
        //                header.put("Host", "http://info.bet007.com");
        //                header.put("User-Agent", "  Mozilla/5.0 (Windows NT 6.1; WOW64; rv:5.0) Gecko/20100101 Firefox/5.0");
        //                header.put("Accept", "  text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8");
        //                header.put("Accept-Language", "zh-cn,zh;q=0.5");
        //                header.put("Accept-Charset", "  GB2312,utf-8;q=0.7,*;q=0.7");
        //                header.put("Connection", "keep-alive");
        //                Connection data = connect.data(header);
        //                try {
        //                    Document document = data.get();
        //                    final Element element = document.select("div[class='content b-txt1']").first();
        //                    Element element1 = document.select("div.articalContent").first();
        
可以加上该网站的主页地址。

## 官方文档

    Document doc = Jsoup.parse(input, "UTF-8", "http://example.com/");
    Elements links = doc.select("a[href]"); //带有href属性的a元素
    Elements pngs = doc.select("img[src$=.png]");
      //扩展名为.png的图片
    Element masthead = doc.select("div.masthead").first();
      //class等于masthead的div标签
    Elements resultLinks = doc.select("h3.r > a"); //在h3元素之后的a元素
    
说明
jsoup elements对象支持类似于CSS (或jquery)的选择器语法，来实现非常强大和灵活的查找功能。.

这个select 方法在Document, Element,或Elements对象中都可以使用。且是上下文相关的，因此可实现指定元素的过滤，或者链式选择访问。

Select方法将返回一个Elements集合，并提供一组方法来抽取和处理结果。

Selector选择器概述

tagname: 通过标签查找元素，比如：a

ns|tag: 通过标签在命名空间查找元素，比如：可以用 fb|name 语法来查找 <fb:name> 元素

#id: 通过ID查找元素，比如：#logo

.class: 通过class名称查找元素，比如：.masthead

[attribute]: 利用属性查找元素，比如：[href]

[^attr]: 利用属性名前缀来查找元素，比如：可以用[^data-]
来查找带有HTML5 Dataset属性的元素
[attr=value]: 利用属性值来查找元素，比如：[width=500]

[attr^=value], [attr$=value], [attr*=value]:

利用匹配属性值开头、结尾或包含属性值来查找元素，比如：[href*=/path/]

[attr~=regex]: 利用属性值匹配正则表达式来查找元素，比如： img[src~=(?i)\.(png|jpe?g)]

*: 这个符号将匹配所有元素

Selector选择器组合使用

el#id: 元素+ID，比如： div#logo

el.class: 元素+class，比如： div.masthead

el[attr]: 元素+class，比如： a[href]

任意组合，比如：a[href].highlight

ancestor child: 查找某个元素下子元素，比如：可以用.body p 查找在"body"元素下的所有 p元素

parent > child: 查找某个父元素下的直接子元素，比如：可以用div.content > p 查找 p 元素，也可以用body > * 查找body标签下所有直接子元素

siblingA + siblingB: 查找在A元素之前第一个同级元素B，比如：div.head + div

siblingA ~ siblingX: 查找A元素之前的同级X元素，比如：h1 ~ p
el, el, el:多个选择器组合，查找匹配任一选择器的唯一元素，例如：div.masthead, div.logo

文档地址：http://www.open-open.com/jsoup/
