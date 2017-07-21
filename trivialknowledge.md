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




# JQuery
- 可视化过滤选择器：
  - `$("div:hidden")`        选择所有的被hidden的div元素    
  - `$("div:visible")`        选择所有的可视化的div元素


- 属性过滤选择器：
  - `$("div[id]")`              选择所有含有id属性的div元素
  - `$("input[name='newsletter']")`    选择所有的name属性等于'newsletter'的input元素
  - `$("input[name!='newsletter']")` 选择所有的name属性不等于'newsletter'的input元素
  - `$("input[name^='news']")`         选择所有的name属性以'news'开头的input元素
  - `$("input[name$='news']")`         选择所有的name属性以'news'结尾的input元素
  - `$("input[name*='man']")`          选择所有的name属性包含'news'的input元素
  - `$("input[id][name$='man']")`    可以使用多个属性进行联合选择，该选择器是得到所有的含有id属性并且那么属性以man结尾的元素


- 子元素过滤选择器：
  - `$("ul li:nth-child(2)")`,`$("ul li:nth-child(odd)")`,`$("ul li:nth-child(3n + 1)")`
  - `$("div span:first-child")`          返回所有的div元素的第一个子节点的数组
  - `$("div span:last-child")`           返回所有的div元素的最后一个节点的数组
  - `$("div button:only-child")`       返回所有的div中只有唯一一个子节点的所有子节点的数组


- 表单元素选择器：
  - `$(":input")`                  选择所有的表单输入元素，包括`input`,` textarea`, `select` 和 `button`
  - `$(":text") `                    选择所有的text input元素
  - `$(":password")`           选择所有的password input元素
  - `$(":radio")`                   选择所有的radio input元素
  - `$(":checkbox")`            选择所有的checkbox input元素
  - `$(":submit")`               选择所有的submit input元素
  - `$(":image")`                 选择所有的image input元素
  - `$(":reset")`                   选择所有的reset input元素
  - `$(":button")`                选择所有的button input元素
  - `$(":file")`                     选择所有的file input元素
  - `$(":hidden")`               选择所有类型为hidden的input元素或表单的隐藏域


- 表单元素过滤选择器：
  - `$(":enabled")`             选择所有的可操作的表单元素
  - `$(":disabled")`            选择所有的不可操作的表单元素
  - `$(":checked")`            选择所有的被checked的表单元素
  - `$("select option:selected")` 选择所有的select 的子元素中被selected的元素


- 选取一个 name 为”S_03_22″的input text框的上一个td的text值
  - `$("input[@ name =S_03_22]").parent().prev().text()`


- 名字以"S_"开始，并且不是以"_R"结尾的    
  - `$("input[@ name ^='S_']").not("[@ name $='_R']")`


- 一个名为 `radio_01`的`radio`所选的值
  - `$("input[@ name =radio_01][@checked]").val()`;


- `$("A B")` 查找A元素下面的所有子节点，包括非直接子节点


- `$("A>B")` 查找A元素下面的直接子节点


- `$("A+B")` 查找A元素后面的兄弟节点，包括非直接子节点


- `$("A~B")` 查找A元素后面的兄弟节点，不包括非直接子节点
