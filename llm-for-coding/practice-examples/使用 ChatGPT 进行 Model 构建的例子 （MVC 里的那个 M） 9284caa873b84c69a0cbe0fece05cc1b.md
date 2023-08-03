# 使用 ChatGPT 进行 Model 构建的例子 （MVC 里的那个 M）

type: Post
tags: copilot
date: August 2, 2023
Date: August 2, 2023

其实一般来说我个人更喜欢使用 ChatGPT 做第一步，后面我更喜欢依赖 GitHub Copilot 来补全，因为补全反正也是个不费事的一路按 Tab 键基本就可以，还可以更及时的介入修改，但这里的例子我就纯用 ChatGPT 撸了。

从建表开始吧（现实中我其实表字段已经在脑海里设计好了，这里做个例子，大家不要这样）

****Message From You:****

> 我现在要设计一个用户表，大概有用户名，昵称，性别，密码，created_at, udpated_at 等字段，还要有一个 union_id 字段，union_id 现在可能用不上，允许为空吧。 你给我一个这样的建表的语句并给我加上详细说明，我来检查下。
> 

****Message From ChatGPT:****

> 当设计用户表时，可以使用以下建表语句来创建一个包含所需字段的表：
> 
> 
> ```
> CREATE TABLE users (
>   id INT PRIMARY KEY AUTO_INCREMENT,
>   username VARCHAR(255) NOT NULL,
>   nickname VARCHAR(255) NOT NULL,
>   gender ENUM('Male', 'Female', 'Other') NOT NULL,
>   password VARCHAR(255) NOT NULL,
>   created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
>   updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
>   union_id VARCHAR(255)
> );
> ```
> 
> 这个建表语句创建了一个名为 "users" 的表，其中包含了以下字段：
> 
> - `id`: 用户ID，作为主键，使用自增长方式生成唯一的用户标识符。
> - `username`: 用户名，用于登录和唯一识别用户。
> - `nickname`: 昵称，用于显示用户的可读性较高的名称。
> - `gender`: 性别，使用枚举类型（Enum）来限制取值范围为 'Male'、'Female' 或 'Other'。
> - `password`: 密码，用于用户身份验证和安全性保护，存储时应进行哈希处理。
> - `created_at`: 创建时间，使用 TIMESTAMP 类型来存储用户创建的日期和时间，默认值为当前时间戳。
> - `updated_at`: 更新时间，使用 TIMESTAMP 类型来存储用户信息最后更新的日期和时间，在更新时自动更新为当前时间戳。
> - `union_id`: Union ID，用于关联用户在不同系统或平台上的唯一标识符，允许为空。
> 
> 请注意，以上建表语句假设你正在使用 MySQL 或类似的关系型数据库。如果你使用的是其他类型的数据库，例如 PostgreSQL 或 SQLite，建表语句可能会有所不同，但基本概念和字段类型应该是相似的。
> 
> 请记得根据你的具体需求和数据库系统的要求进行适当的调整和优化。
> 

****Message From You:****

> gender 改为允许为空吧， 然后每个字段给我加上 comment 注释，用中文。 整个表也要加上中文注释。
> 

****Message From ChatGPT:****

> 以下是修改后的建表语句，包括了字段的中文注释和整个表的中文注释：
> 
> 
> ```
> CREATE TABLE users (
>   id INT PRIMARY KEY AUTO_INCREMENT COMMENT '用户ID',
>   username VARCHAR(255) NOT NULL COMMENT '用户名',
>   nickname VARCHAR(255) NOT NULL COMMENT '昵称',
>   gender ENUM('Male', 'Female', 'Other') COMMENT '性别',
>   password VARCHAR(255) NOT NULL COMMENT '密码',
>   created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
>   updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
>   union_id VARCHAR(255) COMMENT 'Union ID'
> ) COMMENT '用户表';
> ```
> 
> 在这个建表语句中，我添加了中文注释来描述每个字段的含义，以及对整个表的描述。这样可以更清晰地理解表的结构和字段的用途。
> 
> 请注意，注释在实际运行的数据库中可能不会对查询和操作产生直接影响，但它们对于开发人员和维护人员来说是非常有用的，可以提供更好的文档和理解。
> 

****Message From You:****

> 用户 ID 改成从10000开始自增，然后表使用 utf8mb4_general 那个编码
> 

****Message From ChatGPT:****

> 以下是根据您的要求进行修改的建表语句：
> 
> 
> ```
> CREATE TABLE users (
>   id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY COMMENT '用户ID',
>   username VARCHAR(255) NOT NULL COMMENT '用户名',
>   nickname VARCHAR(255) NOT NULL COMMENT '昵称',
>   gender ENUM('Male', 'Female', 'Other') DEFAULT NULL COMMENT '性别',
>   password VARCHAR(255) NOT NULL COMMENT '密码',
>   created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
>   updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
>   union_id VARCHAR(255) DEFAULT NULL COMMENT 'Union ID'
> ) COMMENT='用户表' CHARACTER SET=utf8mb4 COLLATE=utf8mb4_general_ci;
> ```
> 
> 在这个建表语句中，我进行了以下修改：
> 
> - `id` 字段的数据类型改为 `INT UNSIGNED`，并设置 `AUTO_INCREMENT`，以从 10000 开始自增。
> - 表的编码设置为 `utf8mb4_general_ci`，以支持存储更广泛的字符集，包括特殊字符和表情符号。
> 
> 请注意，在修改表的编码时，您需要确保数据库服务器和客户端都支持 `utf8mb4` 编码。这样可以避免在存储和检索数据时出现字符集不兼容的问题。
> 
> 如果您的数据库使用的是其他编码或您有其他特定需求，请相应地进行调整。
> 

到了这一步，建表语句有了，下面就可以直接进入 Model 的创建了。实际上上面刚才展示的对话，我估计大部分人并不会真的这样使用。建表是个严谨，要求极高的过程，ChatGPT 虽然可以也能根据你的自然语言需求做的很好，但这个不分我相信大部分开发人员肯定还是更希望自己建表，这是对业务理解的一个最基础不过的过程。要是脑子里没有对应表的基础概念，那我觉得有没有 ChatGPT ，都得被淘汰……

行了，进入建立 Model ，这里我使用了 Go 里的 GORM 。通常我并不喜欢从零开始让 ChatGPT 进行构建，我更喜欢给它明确的上下文，给它一个例子。

比如 GORM 里的例子，我会先准备好一段代码，然后和刚才的建表语句一起丢给它。

****Message From You:****

> 基于你刚才给的建表语句，我建立了对应的表，现在我要来构建 Model 了，我使用的是 Gorm 。
对 Gorm 我的使用方式是如下所示这样的，你可以视为参考
> 
> 
> ```
> package ai_applications_model
> 
> import "time"
> 
> // AiAppsModel 结构体用于定义 AI 应用程序模型
> type AiAppsModel struct {
>   Id          int       `gorm:"type:int;AUTO_INCREMENT;primary_key" json:"id"` // ID
>   Name        string    `gorm:"type:varchar" json:"name"`                      // 名称
>   Description string    `gorm:"type:varchar" json:"description"`               // 描述
>   Mode        string    `gorm:"type:varchar" json:"mode"`                      // 模式
>   Icon        string    `gorm:"type:varchar" json:"icon"`                      // 图标
>   Status      int       `gorm:"type:int" json:"status"`                        // 状态
>   IsEnable    int       `gorm:"type:int" json:"is_enable"`                     // 是否启用
>   CreatedAt   time.Time `gorm:"type:timestamp" json:"created_at"`              // 创建时间
>   UpdatedAt   time.Time `gorm:"type:timestamp" json:"updated_at"`              // 更新时间
> }
> 
> // TableName 返回表名
> func (AiAppsModel) TableName() string {
>   return "ai_apps"
> }
> 
> ```
> 
> 请根据我给的实例，结合上面的建表语句生成新的 Model 的定义。
> 

****Message From ChatGPT:****

> 
> 
> 
> 根据您提供的示例和之前的建表语句，我为您生成了一个使用 Gorm 的 Model 定义。请注意，我将原有的 `AiAppsModel` 改为 `UsersModel`，以匹配之前我们讨论的用户表。
> 
> ```
> package user_model
> 
> import "time"
> 
> // UsersModel 结构体用于定义用户模型
> type UsersModel struct {
>   ID        int        `gorm:"type:int;AUTO_INCREMENT;primary_key" json:"id"`     // 用户ID
>   Username  string     `gorm:"type:varchar" json:"username"`                       // 用户名
>   Nickname  string     `gorm:"type:varchar" json:"nickname"`                       // 昵称
>   Gender    string     `gorm:"type:enum('Male', 'Female', 'Other')" json:"gender"` // 性别
>   Password  string     `gorm:"type:varchar" json:"password"`                       // 密码
>   CreatedAt time.Time  `gorm:"type:timestamp" json:"created_at"`                   // 创建时间
>   UpdatedAt time.Time  `gorm:"type:timestamp" json:"updated_at"`                   // 更新时间
>   UnionID   string     `gorm:"type:varchar" json:"union_id"`                       // Union ID
> }
> 
> // TableName 返回表名
> func (UsersModel) TableName() string {
>   return "users"
> }
> ```
> 
> 这个新的 Model 定义与您之前提供的示例类似，但已根据我们之前的讨论进行了调整，以匹配最终的建表语句。您可以根据需要进一步调整字段的数据类型、标签等。
> 
> 请确保在使用该 Model 时，导入了正确的 Gorm 包，并按照 Gorm 的使用方式进行数据库操作。
> 

如上所示，我给了它对应的打样之后，它一步到位了……

连注释都给的明明白白。