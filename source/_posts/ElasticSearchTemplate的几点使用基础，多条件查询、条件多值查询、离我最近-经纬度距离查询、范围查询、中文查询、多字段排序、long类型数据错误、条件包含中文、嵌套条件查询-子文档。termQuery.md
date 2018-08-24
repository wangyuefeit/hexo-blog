---
title: >-
  ElasticSearchTemplate的几点使用基础，多条件查询、条件多值查询、离我最近/经纬度距离查询、范围查询、中文查询、多字段排序、long类型数据错误、条件包含中文、嵌套条件查询/子文档。termQuery
categories:
  - Java
tags:
  - Java
  - ElasticSearch
  - ES
date: 2018-08-24 15:43:10
---
多条件查询和条件多值就一起说了：
假如我要用城市编码和多个品牌ID来查询商品列表，那么如下即可：
```
BoolQueryBuilder boolQueryBuilder = boolQuery().must(termQuery("cityCode",searchParams.getCityCode()).must(termsQuery("brandIds", searchParams.getBrandIds()));  
SearchQuery searchQuery =new NativeSearchQueryBuilder().withQuery(boolQueryBuilder).withPageable(pageable).build();  
```
这里通常新手会遇到一个问题，就是如果两个条件中有一个为null，那么就会报错，这时候怎么解决呢？（没有学过SpringData Jpa 和 Hibernate的）网上搜了很久没找到答案，实际上却非常简单，如下即可：
```
BoolQueryBuilder boolQueryBuilder = boolQuery();  
if (null != searchParams.getCityCode()) {  
        boolQueryBuilder.must(termQuery("cityCode", searchParams.getCityCode()));  
}  
if (null != searchParams.getBrandIds() && !searchParams.getBrandIds().isEmpty()) {  
        boolQueryBuilder.must(termsQuery("brandIds", searchParams.getBrandIds()));  
}  
SearchQuery searchQuery =new NativeSearchQueryBuilder().withQuery(boolQueryBuilder).withPageable(pageable).build();  
```
多字段排序：
以前我们可以这样写：
```
Order order1 = new Order(Sort.Direction.ASC, "field1");  
Order order2 =new Order(Sort.Direction.DESC, "field2");  
Sort sort =new Sort(order1, order2);  
```
但是现在会出现：
Sort sort = new ~~Sort~~(order1, order2);  

这种方法已过时，那肯定是不推荐使用了。

可以采用以下方式：
```
Sort sort = new Sort(Sort.Direction.ASC,"field1").and(new Sort(Sort.Direction.DESC, "field2"));  
```
代码手打，有错误请自行更改。

插播一条问题，存储long类型字段，在Kibana上看到的数据不正确：
传了一个订单数据到ES，ID长这样：2018041817382997796，结果传上去之后，用Kibana查出来之后，ES的ID"_id"是对的，就是2018041817382997796，但是订单数据里的orderId却变成了2018041817382997800？？？
就因为这个问题还检查代码，重上数据的找了半天原因。
最终确定原因为Kibana显示数据的问题，增删改查都没有问题，ES也没有问题，就只是Kibana显示有问题，所以这个问题不用去关心。

按“离我最近”举例排序：
这个难度升级了，一步一步来吧：

第一步，准备要使用此排序方式的、要存入ES的Bean，添加位置信息属性，并加 @GeoPointField 。位置属性的类型为GeoPoint。正常情况下应该是用SpringData包下的GeoPoint类型，但是使用期间会出现各种无法判断的错误，所以我们一般自己创建一个GeoPoint类，这个类需要有lat（纬度）和lon（经度）这两个属性、两个构造器（空参、全参），和属性的getter setter。
```
// 自己创建的GeoPoint  
public class GeoPoint {  
  private Double lat, lon;  
  public GeoPoint() {}  
  public GeoPoint(Double lat, Double lon) {  
    this.lat = lat;  
    this.lon = lon;  
  }  
  public Double getLat() {  
    return lat;  
  }  
  public void setLat(Double lat) {  
    this.lat = lat;
  }  
  public Double getLon() {  
    return lon;  
  }  
  public void setLon(Double lon) {  
    this.lon = lon;  
  }  
}  

// 要排序的JavaBean  
public class Shop{  
  @Id  
  private Integer shopId;  
  @GeoPointField  
  private GeoPoint position;  
}  

//多余的就不写了  
```
第二步就是把对象存到ElasticSearch中，这篇文章仅针对“离我最近”排序，如何存入不再赘述。存入之后，在Kibana中看到的位置信息是这样显示的：
```
"shopId":201805111234567,  
  "position":{  
    "lat":40.123,  
    "lon":-70.456  
  }  
```
第三步，就是排序并查询了，代码如下：
```
NativeSearchQueryBuilder nativeSearchQueryBuilder = new NativeSearchQueryBuilder().withQuery(boolQueryBuilder).withPageable(pageable);  

if (null != searchParams.getSortType() && SortTypeConstants.DISTANCE.getCode() == searchParams.getSortType()) {  
  GeoDistanceSortBuilder sortBuilder = SortBuilders.geoDistanceSort("position", "")  
    .point(searchParams.getLat(), searchParams.getLon())  
    .unit(DistanceUnit.METERS)  
    .order(SortOrder.ASC);  
    nativeSearchQueryBuilder.withSort(sortBuilder);  
}  

SearchQuery searchQuery = nativeSearchQueryBuilder.build();  
```
boolQueryBuilder在前面创建好的，用来拼接筛选条件，我的其它文章有介绍。按距离排序需要创建NativeSearchQueryBuilder，然后.withSort()就可以了。
经纬度和排序类型都是放在searchParams里面的，这个没有死规定，可以自行定义。
这样就完成了“离我最近”的排序。
如果想实现举例范围的，结合后面的范围查询。

范围查询：
(1) gte() :范围查询将匹配字段值大于或等于此参数值的文档。
(2) gt() :范围查询将匹配字段值大于此参数值的文档。
(3) lte() :范围查询将匹配字段值小于或等于此参数值的文档。
(4) lt() :范围查询将匹配字段值小于此参数值的文档。
(5) from() 开始值 to() 结束值 这两个函数与includeLower()和includeUpper()函数配套使用。
(6) includeLower(true) 表示 from() 查询将匹配字段值大于或等于此参数值的文档。
(7) includeLower(false) 表示 from() 查询将匹配字段值大于此参数值的文档。
(8) includeUpper(true) 表示 to() 查询将匹配字段值小于或等于此参数值的文档。
(9) includeUpper(false) 表示 to() 查询将匹配字段值小于此参数值的文档。

例如：
```
RangeQueryBuilder rangeQueryBuilder = QueryBuilders.rangeQuery("createTime").gte(1527580006000).lt(1527692129000);
```
或
```
RangeQueryBuilder rangeQueryBuilder = QueryBuilders.rangeQuery("createTime");
rangeQueryBuilder.from(1527580006000);
rangeQueryBuilder.to(1527692129000);
rangeQueryBuilder.includeLower(true);
rangeQueryBuilder.includeUpper(true);
```
来筛选一段时间内的数据。写完这句之后，把它withQuery到QueryBuilder后面就可以了。

或者也可以直接像文章开头那样拼接：
```
.must(rangeQuery("createTime").gte(1527580006000).lt(1527692129000));
```
中文条件查询：
比如：
```
termQuery("cityName", "北京市");  
```
改成：
```
termQuery("cityName.keyword", "北京市");  
```
即可。

嵌套条件查询：
直接拼接：
```
.must(nestedQuery("userInfo",termQuery("userInfo.phone", "17******084"), ScoreMode.None));
```
如果要做筛选可以把ScoreMode改为其它，这里不做筛选操作直接用None。

ScoreMode的值包括：Avg、Max、Total、Min。看一眼也知道什么意思了吧。

这个在网上翻了一下午没翻到，最后在提示代码中找到了，也是不容易。希望以后有人用到，可以直接翻到我的文章。

如果本文对你有用，麻烦点个喜欢。有钱的捧个钱场，没钱的回家取点钱来捧个钱场，谢谢大家。

