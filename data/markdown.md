

谷歌渲染公式API文档：
https://developers.google.com/chart/infographics/docs/formulas?hl=en

例子：
![](https://chart.googleapis.com/chart?cht=tx&chl=x%20=%20%5Cfrac%7B-b%20%5Cpm%20%5Csqrt%20%7Bb%5E2-4ac%7D%7D%7B2a%7D)

atom MPP插件，支持latex公式编辑和渲染。再配合google api的latex公式渲染。

http://chart.googleapis.com/chart?cht=tx&chl=这里填写LaTex公式
  - `chs=<width>x<height>` 可选，控制图片大小，如果只指定一个数字，表示高度，宽度自动生成
  - `cht=tx` 必须，指定图片类型为formula公式
  - `chl=<data>` 必须，Tex公式
  - `chf` background fill type
  - `chco` text color


|

![E=mc^2](http://chart.googleapis.com/chart?cht=tx&amp;chl=E=mc^2)


* https://wo1fsea.github.io/2016/11/26/Survival_Skill_Markdown_and_LaTex_Formulas/
