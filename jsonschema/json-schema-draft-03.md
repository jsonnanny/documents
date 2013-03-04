#用于描述JSON文档结构及含义的JSON媒体类型#

##概要##
JSON(Javascript Object Notation) Schema 是对“application/schema+json”的定义，此文档类型以JSON数据格式为基础。从当前应用场景来看，JSON Schema所能做的主要是对数据形式以及操作方式进行约束，同时也包括对校验、文档、链接导航、访问控制的定义。

##文档状态##
   
此**互联网草案**以符合BCP 78与BCP 79规定的方式进行提交。

所谓**互联网草案**是指**互联网工程工作组**的工作文档，需要注意的是其他工作组也可以将此文档以**互联网草案**的形式分发。可访问[http://datatracker.ietf.org/drafts/current/](http://datatracker.ietf.org/drafts/current/)查看当前已存在的**互联网草案**。

此文档失效时间为二零一一年五月二十六日。

##版权声明##
Copyright (c) 2010 版权归互联网工程工作组以及作者所有。

在有效期内，此文档受BCP 78以及互联网工程工作组相关文档条例[http://trustee.ietf.org/license-info](http://trustee.ietf.org/license-info)保护。请仔细阅读以上条例内容中关于权利与约束条件内容。从此文档中提取的任何代码片段需要包含简化BSD许可证中章节4.e中的内容。

##一、介绍##
JSON(Javascript Object Notation)Schema是对JSON类型数据结构的定义。从当前应用场景来看，JSON Schema所能做的主要是对数据形式以及操作方式进行约束，同时也包括对校验、文档、导航、访问控制的定义。

##二、 约定##
在此文档中出现的“**必须**”，“**不允许**”，“**必需**”，“**应该**”，“**不应该**”，“**推荐**”，“**可能**”，“**可选**”等与RFC 2119[RFC2119]中的定义相一致。

##三、概括##
JSON Schema基于JSON，与通常我们所使用的JSON文档相比，它所定义的媒体类型"application/json+schema"通过设定约束值，描述资源以及表达多资源间的关系等方式来表现JSON文档结构。

JSON Schema由多个独立的规范组成。核心规范部分主要用于对JSON结构以及结构中元素的有效性进行阐述，而Hyper Schema部分则主要阐述的是结构中如何以某种类似超链接的元素来是如何表达多资源间的关系，从而使终端设备可以操控对多个JSON文档。

其他JSON Schema则是用于约束某种具体数据类型中属性值的元文档。

以描述某产品的JSON Schema为例:

    {
        "name":"Product",
        "properties":{
            "id":{
                "type":"number",
                "description":"Product identifier",
                "required":true
            },
            "name":{
                "description":"Name of the product",
                "type":"string",
                "required":true
            },
            "price":{
                "required":true,
                "type": "number",
                "minimum":0,
                "required":true
            },
            "tags":{
                "type":"array",
                "items":{
                    "type":"string"
                }
            }
        },
        "links":[
            {
                "rel":"full",
                "href":"{id}"
            },
            {
                "rel":"comments",
                "href":"comments/?id={id}"
            }
        ]
    }

上面例子中的schema描述了一个JSON文档实例中哪些属性（id，name，price）是必要的，而哪些属性(tags)是可选的。链接则表达的是JSON文档实例之间的关系。

###3.1  术语###
在本规范中，**schema**专指基于JSON Schema的定义；**实例**专指schema所描述或校验的JSON值。

###3.2  设计考虑因素###
JSON Schema所做的不止是对JSON对象数据在结构上进行规范约束，同时也对数据如何解析、校验进行定义，在考虑扩展性的前提下，终端设备才有可能在解析JSON文档中的结构以及超链接信息时保持足够的准确度。另外，JSON文档中数据结构复杂多变的特征也要求schema的高的扩展性。

本规范与协议无关。底层协议（例如：HTTP）定义的是客户端与服务器端接口语义，做获取资源文件以及资源变动之用。而JSON schema的主要目标则不过是描述那些基于底层协议架构之上的服务所生成的复杂JSON数据的结构。

##四、Schema与实例##
JSON Schema与它相对应的实例是描述与被描述的关系。实例的媒体类型可以为"applcation/json"或者其他子类型。如何指定schmea不在本规范的论述范围。最好为实例文档中指定schema，因为这样终端设备就可以解析JSON实例以及包含的一些自描述信息并过滤到不需要的实例数据。关联实例与schema的方法有二，通过设定profile指定MIME，或者在链接头中设定describeby指向对应的schema。

例如：

    Content-Type: application/my-media-type+json;
    			  profile=http://json.com/my-hyper-schema

如果在使用某中协议传输（例如HTTP）中已经指定了头部信息，可以在链接头中指定schema：

    Link: <http://json.com/my-hyper-schema>; rel="describedby"

单个实例**可能**存在多个schema，那么实例数据**应当**通过其对应所有schema的校验。如果JSON文档是多个实例的集合，那么这个集合所包含的实例就**可能**存在多个不同schema，这样就**可能**需要在schema中设置pathStart属性来界别其应用在集合中的哪个实例。总而言之，schema的引用机制因实例文档的类型不同会存在差异。

###4.1  JSON Schema 的自描述###
JSON Schema可以实现自描述。核心规范和Hyper规范都是自描述的：

核心规范最新版：
>http://json-schema.org/schema

核心规范03版：
>http://json-schema.org/draft-03/schema

Hyper规范最新版：
>http://json-schema.org/hyper-schema

Hyper规范03版：
>http://json-schema.org/draft-03/hyper-schema
   
所有使用某一包含媒体类型定义传输协议的schema**应该**包含一个MIME参数并将其值指向自描述型的Hyper Schema或者基于Hyper Schema的扩展型schema：
    
    Content-Type: application/json;
    			  profile=http://json-schema.org/draft-03/hyper-schema

##五、核心规范定义##
JSON Schema本质上是定义多个键值对的JSON对象。JSON Schema允许递归，其结构中允许出现嵌套。

以下面的JSON Schema为例:

    {
        "description": "A person",
        "type": "object",
        "properties": {
            "name": {
                "type": "string"
            },
            "age": {
                "type": "integer",
                "maximum": 125
            }
        }
    }

JSON Schema 对象可以包含下面描述的属性，我们将其定义为schema属性(所有的属性均为可选)：

###5.1 type###
此属性对实例的原始类型或schema进行约束定义(**必须**)，其值可以为下列两种形态中的任意一种：

####简单类型####
表示原始/简单类型的字符串，允许值如下：

* string  值**必须**为字符串；
* number 值**必须**为整型（数字类型的子集）；
* boolean 值**必须**为布尔值；
* object 值**必须**为对象；
* array 值**必须**为数组；
* null 值**必须**为 null，需要注意的是，只有在使用复合(union)类型（被结合类型包含）时才允许使用；
* any 值**可能**为包含null类型的任何值；

如果属性不在列表中或是未定义，则值可以为**any**类型。出于自定义的目的，**可能**会有其他类型值，不过规范只实现了最小校验，因此任何未知类型的值的出现都是合法的。

####复合类型####
包含两个及以上简单类型定义的数组。数组中的每项均**必须**为一个简单类型定义或者schema。如果实例值符合其中任一简单类型定义或者任一schema都会被认为是合法的。

如下例，schema中定义的是实例值为string或number型任意一种定义的场景：

	{"type":["string","number"]}

###5.2 properties###
此属性用于约束定义**实例对象**中的值。当实例的**type**为**object**时，实例对象**必须**符合此对象中的属性定义。并且在此对象中，每项定义都**必须**是一个**schema**，且属性的键**必须**与实例对象中定义的属性名称保持一致。实例值**必须**通过此属性定义的校验。在此属性中的每项顺序**可能**与实例属性中的顺序不一致，这就是说此属性与顺序无关。

###5.3 patternProperties###
此属性同**properties**一样，用于约束定义实例对象的值。所不同是的，对象中的每个属性名称都是一个正则表达式（Perl形式），值则为一个schema。如果和属性名称中的正则表达式匹配到实例对象中的某属性名称。那么实例对象的属性**必须**符合对应正则表达式名称所对应schema。

###5.4 additionalProperties###
此属性为那些在对象类型中未被明确定义的属性提供一个schema。如果设定此属性，其值**必须**为一个schema或者布尔型（boolean）。如果值为false，任何附加属性将不会覆盖schema中的定义。此属性默认值为**空schema**(empty schema)，即附加属性允许为任何值。

###5.5 items###
此属性主要用于约束实例数组项的值。默认值为空**schema**，即实例数组项可以为任何值。

当此属性值为单个schema且实例值为数组时，则数组中的每项都**必须**此schema的校验。

当此属性值为一包含多个schema的数组且实例值为数组时，实例数组中每项都**必须**符合在此属性中相同下标值对应的schema。我们称之为元组(tuple)类型。在使用元组类型时，附加项是否允许出现以及addtionalItems(章节5.6)的约束关系与addtionalProperties（章节5.4）规则保持一致。

###5.6 additionalItems###
如果**items**中出现元组类型时，此属性用于定义数组实例附加项。如果值为false，则表示数组中出现的附加项是非法的。如果值为schema，则表示的是附加项所对应的schema。

###5.7 required###
此属性表示是否需要设定实例值且实例不能为undefined。默认值为false，表示此实例可选。

###5.8  dependencies###

此属性类型为object，用于表示实例对象中的属性依赖。如果在对象实例中包含此属性对象中某属性同名的情况，那么此实例就必须符合相对应属性的值（**依赖值**）。

依赖值可以是以下两种形态中任意一种：

**简单依赖** 如果依赖值为字符串，则实例对象就**必须**包含与此依赖值同名的属性。如果依赖值为一字符串数组，则实例对象就**必须**包含与依赖值数组中每一个同名的属性。

**Schema 依赖** 如果依赖值为schema，则实例对象**必须**符合此schema。

###5.9 minimum###
此属性用于定义当实例为数字类型时的实例的最小值。

###5.10 maximum###
此属性用于定义当实例为数字类型时实例的最大值。

###5.11 exclusiveMinimum###
此属性用于标识当实例为数字类型时实例值是否可以为minium中设定的最小值。默认值为false，即实例值可以大于等于最小值。

###5.12 exclusiveMaximum###
此属性用于标识当实例为数字类型时实例值是否可以为maxinum中设定的最大值。默认值为false，即实例值可以小于等于最大值。

###5.13 minItems###
此属性用于定义当实例为数组类型时所包含的最少项。
  
###5.14 maxItems###
此属性用于定义当实例为数组类型是所包含的最多项。

###5.15 uniqueItems###
此属性用于标识当实例为数组类型时数组的每项是否**必须**是唯一的。如果两个实例出现下列情况中的任意一种即被认为是相等的：

* 值都为null；
* 类型为布尔、数字或者字符串，值相等；
* 类型为数组，数组长度相等且差集为空；
* 类型为对象，含有相同个数的同名键值对；

###5.16 pattern###
此属性为一正则表达式，当实例为字符串类型时，字符串实例必须匹配此属性提供的正则表达式。此处的正则表达式**应该**符合正则表达式规范（Perl形式）。

###5.17 minLength###
此属性定义当实例为字符串类型时字符串的最小长度。

###5.18 maxLength###
此属性定义当实例值为字符串类型时字符串的最大长度。

###5.19 enum###
此属性**必须**为数组类型，用于枚举实例属性的可能值。定义此属性后，实例值必须是此数组属性中的任意一符合schema的值。枚举值与uniqueItems中定义的规则（章节5.15）保持一致。

###5.20 default###
此属性定义当实例为undefined时的默认值。

###5.21 title###
此属性为字符串类型，用于简短描述实例属性。

###5.22 description###
此属性为字符串类型，用于完整描述实例属性。

###5.23 format###
此属性用于定义实例属性值可能会出现数据类型，内容类型以及微格式。此属性**可能**是下面列表中的某一种，如果是，那么这些格式自身就附带了语义。这些格式应用场景**应当**只限于当值为原始类型（字符串，整型，数字，布尔）时。校验器**可以**(非必要)根据格式校验实例值。预设值如下：
 
* date-time 此值表示实例值**应该**符合形如YYYY-MM-DDThh:mm:ssZ的ISO 8601的UTC时间表示格式，推荐使用此格式表示日期和时间戳；
* date 此值表示实例值**应该**符合形如YYYY-MM-DD的格式。在只需要表示日期的情况下，可选择此值，其他情况则推荐使用date-time；
* time 此值表示实例值**应该**符合形如hh:mm:ss的格式。在只需要表示时间的情况下，可选择此值，其他情况则推荐使用date-time；
* utc-millisec 此值较为特殊，表示实例值**应该**为距UTC时间1970年1月1日0点0分的类型为整型或浮点型的毫秒值；
* regex 此值实例值应该为正则表达式（Perl形式）；
* color 此值表示实例值**应该**符合CSS 2.1[W3C.CR-CSS21-20070719]的色彩表现方式；
* style 此值表示实例值**应该**符合CSS 2.1[W3C.CR-CSS21-20070719]的样式规则（例如："color: red; background-color:#FFF"）；
* phone 此值表示实例值**应该**为电话号码；
* uri 此值表示实例值**应该**为URI；
* email 此值表示实例值**应该**为邮件地址；
* ip-address 此值表示实例值**应该**为ipv4地址；
* ipv6 此值表示实例值**应该**为ipv6地址；
* host-name 此值表示实例值**应该**为主机名称;

如果存在已创建的自定义格式，那么**可以**将此值设置为URI形式，且此URI指向此格式的schema。

###5.24.  divisibleBy###
此属性定义数字实例可以整除的除数。此值**不应该**为0。

###5.25 disallow###
此属性的可取的值与type属性一致，如果实例type值匹配此类型（属性值为字符串）或者匹配类型中的某一种(属性值为包含多个类型的数组)，那么此实例就是非法的。

###5.26 extends###
此属性**必须**为当前schema所继承的底层schema，任何符合当前schema的实例也**必须**符合引用的底层schema。属性**可以**是一个数组，在此场景下，实例**必须**符合数组中的每个schema。一个schema可以在另外一个schema的基础上添加附加属性，新增或修改约束条件。

从概念上讲，这里的扩展可以理解为实例的所有约束来自当前schema与所继承的schema。合并多个schema的行为尽管不是必要的，但确实是一种更优的实现。例如：

    {
        "description":"An adult",
        "properties":{"age":{"minimum": 21}},
        "extends":"person"
    }
    {
        "description":"Extended schema",
        "properties":{"deprecated":{"type": "boolean"}},
        "extends":"http://json-schema.org/draft-03/schema"
    }

###5.27 id###
此属性用于定义当前schema的URI，直白讲就是指向"self"的链接。URI**可以**是相对或绝对路径。当URI为相对路径时，如果其以包含在父级的schema中出现，则其根目录为父级schema的根目录保持一致，反之，则当前schema及其父级schema的URI都将指向当前schema的地址。在id属性没有设置的情况下，当前schema的URI将会被父级schema的URI替代。当前schema的URI通常用于构造其他引用的相对路径（例如$ref）。

###5.28 $ref###
此属性值为指向当前schema所对应的完整表现的URI。当校验器处理此属性时，当会将当前的schema替换为此属性的值后再对实例进行校验。此URI**可以**是相对或绝对路径。相对路径情景下URI的根目录与当前schema所在目录相同。

###5.29 $schema###
此属性用于表示当前schema的标准schema。此属性被设定时，校验器**应当**使用此属性值设定的URI所指向的schema去解析Hyper Schema中的links属性（章节6.1）。

校验器**可以**通过此属性的值来确定当前schema所使用的标准schema的版本，从而提供正确的校验行为和特征。因此，**推荐**所有的schema作者在其编写的schema中设定此属性以避免因标准schema版本差异引发的问题。

##六、 Hyper Schema##
本章所涉及的属性是对核心规范中已出现属性的补充，主要为终端设备提供JSON数据资源之间的关系。同核心规范中的属性一样，Hyper Schema中提到的所有属性都是可选的。因此空对象也被认为是合法的schema，因为从本质上讲它就是无任何约束关系的JSON文本。

###6.1 links###
此属性值**必须**为一数组，且数组中的每项都是用于描述实例之间链接关系的链接对象。

####6.1.1 链接描述对象####
链接描述对象常用做描述链接关系，在schema的上下文环境中，则用作定义同schema下多个实例间的链接关系，并且实例值还可以将其参数化。通常，链接描述可以为自身(非schema文档)，也可以是标准的描述schema或者是为此数据结构声明的schema。

标准的描述schema链接地址如下：

* [http://json-schema.org/links](http://json-schema.org/links)(最新版本)  
* [http://json-schema.org/draft-03/links](http://json-schema.org/draft-03/links) (03版本).

#####6.1.1.1 href#####
此属性值为指向相关资源的目标URI。实例属性值**应该**为形如[RFC3986](http://tools.ietf.org/html/rfc3986)中定义的URL-Reference格式。当links出现在schema中时，若href值为相对链接，那么其根目录**应该**与实例对象而不是当前schema所在的目录相同。另外，当links出现在在schema中且在URI模板中出现的参数在实例对象中有对应键值或者其他诸如用户输入等数据源的情况下，URI**应该**会被实例对象中的属性值参数化。参数化的过程就是在URI中"{}"所包含的字符串被对应实例属性值替换输出完整URI的过程。

例如，某href值的定义：

	http://somesite.com/{id}

其中的{id}将会被替换为实例对象中属性id的值。假如实例id属性值为字符串45，那么扩展后完整的URI就是：
	
	http://somesite.com/45 

如果大括号中包含字符串@，那么就是当前整个实例而不是单个属性替换掉大括号中的字符串。通常只有在实例为原始类型（字符串，布尔，数字）时才会有这样的场景。

#####6.1.1.2 rel#####
此属性值用于标识实例与目标之间的关系名称。与目标的关系需要明确表达的不止是父包含子、子继承父的关系，实例对象与schema，sub-schema之间的关系也**应该**解释清楚。如果一个JSON类型的资源以链接的形式包含一个类型为对象的属性，那么子对象就和此资源建立了关系。另外资源与描述资源的schema之间的关系也**必须**明确标识。

关系定义**应该**与媒体类型**无**依赖关系。尽管鼓励用户利用已有被广泛接受的关系定义，包括已存在的关系注册[RFC4287](http://tools.ietf.org/html/rfc4287)。不过我们已经定义了清晰的处于JSON Hyper Schema上下文的关系定义：

* self 当links属性在实例对象中，则表示实例对象被指定URI所表示的目的资源识别为自身的完整表现；
* full 表示链接指向的目标资源是实例对象的完整表现，但包含当前links属性的对象则不一定是完整的；
* describedby 表示目标链接是当前实例对象所对应的schema，主要用于表现schema的继承，多态，中间件等JSON数据结构；
* root 表示目标链接为了方便终端进行交互、拆分管理被看做根或者表现主体，实例对象中的其他属性则是用于描述数据的源数据。
      
下面的关系定义主要用于表示schema之间的关系:

* instances 表示目标资源是schema的实例集合；
* create 表示目标**应该**是用于创建基于此schema的实例的一个使用非安全方式提交的链接(例如POST)；

例如某schema定义如下:

    {
        "links": [{
            "rel": "self",
            "href": "{id}"
        },
        {
            "rel": "up",
            "href": "{upId}"
        },
        {
            "rel": "children",
            "href": "?upId={id}"
        }]
    }


已取得实例资源集合的JSON表现：

	GET /Resource/

    [{
        "id": "thing",
        "upId": "parent"
    },
    {
        "id": "thing2",
        "upId": "parent"
    }]

以集合中的第一项为例，self指向的URI为"/Resource/thing"，up指向的URI为"/Resource/parent"，而children指向的URI为"/Resource/?upId=thing"。
  
#####6.1.1.3 targetSchema#####
此属性值为用户定义当前JSON数据结构的schema链接。

#####6.1.1.4 提交链接属性 #####
下面提到的属性同样应用与链接定义对象，不过其主要目的是为了模拟一个类似HTML表单便于向服务器端提供一些额外的数据，而这些数据通常是有用户提供的。

######6.1.1.4.1 method######
此属性用于定义访问目标资源的method，在HTTP环境中，其值可以为GET或者POST，类似PUT、DELETE等带有明显语义的HTTP方法不需要在此处定义。默认值为GET。

######6.1.1.4.2 enctype######
若设置此属性，则表示服务端在查询提交实例集合时所支持的查询媒体类型格式。此类查询会将此属性值以后缀的形式附加在目标URI后去查询服务端返回的基于属性约束的集合或者提交数据到目标资源。

以下面的schema为例：

    {
        "links": [{
            "enctype": "application/x-www-form-urlencoded",
            "method": "GET",
            "href": "/Product/",
            "properties": {
                "name": {
                    "description": "name of the product"
                }
            }
        }]
    }
    
客户端将会去服务端查询特定name的实例：
	
	/Product/?name=Slinky

如果enctype与method都没有指定的话，只会根据href中指定的URI向服务端发送请求。当method值为POST时，默认媒体类型为"application/json"。

######6.1.1.4.3 schema######
此属性包含一个用于约束定义提交请求的数据结构，在GET请求中，此schema定义查询字符串所需的属性，而在POST请求中，则会在主体部分被定义。
   
###6.2 fragmentResolution###
此属性表示为了处理基于URI方式的资源引用而采用的片段分辩协议，这里提到的URI包括实例对象与子级实例对象。默认的碎片分辩协议是"斜线分割"，也是接下来我们要阐述的。其他片段分辩协议也**可能**会被用到，但不在本规范的讨论范围内。片段识别基于[RFC2396](http://tools.ietf.org/html/rfc2396)，其主要定义的是一套文档内容引用机制。

####6.2.1 slash-delimited(斜线分割)型片段分辩协议####
所谓斜线分割的片段分辩协议，主要是通过使用"/"（\x2F）对片段标记进行分割后将其转化为一套的属性引用指令（token），而每个属性引用指令都是直接由URI字符或由经过脱字处理(escape)过的URI字符组成，它会被解析成JSON结构中的引用。最终获取目标值的过程是，首先，对URI片段进行分析得出属性引用指令，然后从JSON结构的根节点开始，逐个使用属性引用指令获取目标值，如果获取的目标是一个JSON对象，那么就接下来就获取此对象上属性名为下个指令标识的属性值，如果获取的目标是一个数组，那么接下来就获取此数组中下个指令所表示的下标值（指令此时必须为数字）所对应的项，如此类推，只至处理完所有属性引用指令。

属性名称**应该**经过URI编码处理，尤其需要注意的是，当属性名称中出现"/"**必须**经过编码才能避免被识别为属性分隔符。

以下面的JSON数据为例：

    {
        "foo": {
            "anArray": [{
                "prop": 44
            }],
            "another prop": {
                "baz": "A string"
            }
        }
    }

片段标识及分辩结果：

<table style="width:100%;">
<tr>
	<th>片段标识</th>
	<th>分辩结果</th>
</tr>
<tr>
	<td>#</td>
	<td>自身，资源根节点</td>
</tr>
<tr>
	<td>/foo</td>
	<td>foo属性</td>
</tr>
<tr>
	<td>/foo/another%20prop/baz</td>
	<td>anthor prop中baz所对应的字符串，即"A String""</td>
</tr>
<tr>
	<td>/foo/anArray/0</td>
	<td>foo中anArray的下标值为0的项（第一项）。</td>
</tr>
</table>

####6.2.2  dot-delimited（点分割）型片段分辩协议####

点分割的片段分辨协议与斜线分割的片段分辩协议不同之外在与在分割符上的区别，点分割的片段分辩协议的分割符为"."（\x2E），而其他大部分特征都是相同的。

###6.3 readonly###

此属性用于标识实例是否**不应当**发生改变。终端设备对此属性的修改可能会被服务器拒绝。
  
###6.4 contentEncoding###
此属性定义在实例属性为字符串的情况下，二进制字符串会以何种编码进行解码。[RFC2045](http://tools.ietf.org/html/rfc2045)章节6.1列出此属性的可取值。
 
###6.5.  pathStart###
此属性值为URI，用于定义以此URI为起始的实例才会校验。此属性值**必须**根据[RFC3986](http://tools.ietf.org/html/rfc3986)相对实例URI进行设定。
   
当一个实例引用多个schema时，终端设备可以通过pathStart属性值来判断schema在实例中的适用范围。如果实例的URI不是以此属性值为起始或其他指定起始URI的schema不再匹配此实例，那么这个schema就**应该**应用在此实例上。任何不包含pathStart的schema应用范围将会是引用它的实例全部。
  
###6.6 mediaType###
此属性用于定义实例的媒体类型。

##七、安全因素##
This specification is a sub-type of the JSON format, and consequently the security considerations are generally the same as RFC 4627[RFC4627].  However, an additional issue is that when link relation of "self" is used to denote a full representation of an object, the user agent SHOULD NOT consider the representation to be the authoritative representation of the resource denoted by the target URI if the target URI is not equivalent to or a sub-path of the the URI used to request the resource representation which contains the target URI with the "self" link.  For example, if a hyper schema was defined:
    
    {
    	"links":[
           {
             "rel":"self",
             "href":"{id}"
           }
    	]
    }
      
 And a resource was requested from somesite.com:

	GET /foo/

With a response of:

	Content-Type: application/json; profile=/schema-for-this-data
	
    [{
        "id": "bar",
        "name": "This representation can be safely treated as authoritative"
    },
    {
        "id": "/baz",
        "name": "This representation should not be treated as authoritative the user agent should make request the resource from " /baz " to ensure it has the authoritative representation"
    },
    {
        "id": "http://othersite.com/something",
        "name": "This representation should also not be treated as authoritative and the target resource representation should be retrieved for the authoritative representation"
    }]

##八、IANA注意事项##
The proposed MIME media type for JSON Schema is "application/schema+json".

    Type name: application

    Subtype name: schema+json

    Required parameters: profile

   The value of the profile parameter SHOULD be a URI (relative or absolute) that refers to the schema used to define the structure of this structure (the meta-schema). Normally the value would be http://json-schema.org/draft-03/hyper-schema, but it is allowable to use other schemas that extend the hyper schema's meta- schema.

    Optional parameters: pretty

The value of the pretty parameter MAY be true or false to indicate if additional whitespace has been included to make the JSON representation easier to read.

###8.1 Registry of Link Relations###
This registry is maintained by IANA per RFC 4287 [RFC4287] and this specification adds four values: "full", "create", "instances","root".  New assignments are subject to IESG Approval, as outlined in RFC 5226 [RFC5226].  Requests should be made by email to IANA, which will then forward the request to the IESG, requesting approval.
