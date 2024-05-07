# XPath

## XPath节点

### XPath术语

#### 节点

七种节点：元素、属性、文本、命名空间、处理指令、注释、文档(根)节点

**exp：**

````html
<?xml version="1.0" encoding="UTF-8"?>

<bookstore>
  <book>
    <title lang="en">Harry Potter</title>
    <author>J K. Rowling</author>
    <year>2005</year>
    <price>29.99</price>
  </book>
</bookstore>
````

**节点：**

```html
<bookstore> (文档节点)

<author>J K. Rowling</author> (元素节点)

lang="en" (属性节点)
```

#### 基本值(原子值，Atomic value)

无父或无子的节点

**exp：**

```
J K. Rowling

"en"
```

#### 项目(Item)

基本值或节点

