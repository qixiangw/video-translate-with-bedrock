# 使用Amazon BedRock和Amazon Lambda实现视频字幕无服务器自动翻译
利用Amazon BedRock和Amazon Lambda构建一个无服务器的视频字幕自动翻译解决方案，该方案不仅能够提供高质量的翻译结果，还能够实现自动化和按需扩展，为用户提供便捷、高效的字幕翻译服务。

在这个无服务器自动翻译解决方案中，我们利用了两个关键的AWS服务：Amazon Bedrock和AWS Lambda。这两项服务的结合为构建高效、可扩展的视频字幕翻译系统提供了强大的基础。

## 服务介绍
Amazon Bedrock 是一项完全托管的服务，通过单个 API 提供来自 AI21 Labs、Anthropic、Cohere、Meta、Mistral AI、Stability AI 和 Amazon 等领先人工智能公司的高性能基础模型（FM），以及通过安全性、隐私性和负责任的人工智能构建生成式人工智能应用程序所需的一系列广泛功能。使用 Amazon Bedrock可以轻松试验和评估适合的热门 FM，通过微调和检索增强生成（RAG）等技术利用企业自有数据对其进行私人定制，并构建使用企业系统和数据来源执行任务的代理。由于 Amazon Bedrock 是无服务器的，因此无需管理任何基础设施，并且可以使用已经熟悉的 AWS 服务将生成式人工智能功能安全地集成和部署到已有的应用程序中。

AWS Lambda 是一项计算服务，无需预配置或管理服务器即可运行代码。借助 AWS Lambda，几乎可以为任何类型的应用程序或后端服务运行代码，并且不必进行任何管理。AWS Lambda 在可用性高的计算基础设施上运行代码，执行计算资源的所有管理工作，其中包括服务器和操作系统维护、容量预置和自动扩展、代码监控和记录。

Amazon Simple Storage Service (Amazon S3) 是一种对象存储服务，提供行业领先的可扩展性、数据可用性、安全性和性能。这意味着各种规模和行业的客户都可以使用它来存储和保护各种用例（如网站、移动应用程序、备份和还原、存档、企业应用程序、IoT 设备和大数据分析）的任意数量的数据。

## 解决方案整体架构

1. 用户上传视频和原语言的SRT格式的字幕文件到 S3 存储桶；
2. 监测到 S3 存储桶的事件变化（put object操作，srt文件类型），自动触发执行 Lambda 函数；
3. Lambda 函数做文件处理，调用服务，生成并上传翻译结果；
    1. 对SRT文件做解析分离字幕文件中的序号，时间戳，文字部分
    2. 定义翻译相关Prompt，比如指定翻译风格，翻译目标语言
    3. 调用 Amazon Bedrock服务对字幕文件的文字内容进行翻译
    4. 将翻译结果，以及时间戳合并形成一个目标语言的 SRT 字幕文件
    5. 新的字幕文件上传到S3存储桶
4. 用户可以利用原来的视频文件和目标语言的 SRT 字幕文件

## 部署说明

/deploy 文件夹中提供了基于Cloudformation一键部署的方式，包括新建一个用于存储字幕文件的S3桶，以及一个用于部署翻译应用的lambda函数；
/src 文件夹中有字幕翻译相关的python 和 Lambda原始代码，方便自定义部署的时候根据自己的需求更改代码；