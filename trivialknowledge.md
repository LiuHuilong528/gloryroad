# Java
* ajax 传数组到后台：

``` javascript
var arr=[];
arr.push({"nam":"888"});
arr.push({"nam":"888"});
arr.push({"nam":"888"});
$.ajax(
    url,
    arr,//这里就是传给后台的数据——直接是数组；
    success,
    'json'
);

```

java后台：

``` java
//struts中
private List<String> name = new ArrayList<String>();
//这样就可以接到数据了
```