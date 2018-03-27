powershell

IDE:Windows PowerShell ISE
- 自动完成
- 脚本交互
- 调试
- 远程调试

Win+R 输入 powershell 进入shell命令行界面。脚本文件后缀名为.ps1。

获取帮助：
get-help command
  - get-help get-executionpolicy

脚本后缀：
.ps1


get-executionpolicy
set-executionpolicy remotesigned -Scope CurrentUser -Force
  - Restricted 默认设置，不允许任何脚本运行
  - RemoteSigned 运行未签名脚本
  - Unrestricted


```
# create new excel instance
$objExcel = New-Object -comobject Excel.Application
$objExcel.Visible = $True
$objWorkbook = $objExcel.Workbooks.Add()
$objWorksheet = $objWorkbook.Worksheets.Item(1)

# write information to the excel file
$i = 0
$first10 = (ps | sort ws -Descending | select -first 10)
$first10 | foreach -Process {$i++; $objWorksheet.Cells.Item($i,1) = $_.name; $objWorksheet.Cells.Item($i,2) = $_.ws}
$otherMem = (ps | measure ws -s).Sum - ($first10 | measure ws -s).Sum
$objWorksheet.Cells.Item(11,1) = "Others"; $objWorksheet.Cells.Item(11,2) = $otherMem

# draw the pie chart
$objCharts = $objWorksheet.ChartObjects()
$objChart = $objCharts.Add(0, 0, 500, 300)
$objChart.Chart.SetSourceData($objWorksheet.range("A1:B11"), 2)
$objChart.Chart.ChartType = 70
$objChart.Chart.ApplyDataLabels(5)
```
保存为CPUPie.ps1，右键单击选择run with powershell。打开Excel，画出占用内存最高的10个进程饼图。



cmdlet + regex + pipeline

显示占用内存最大的10个进程：
ps | sort ws -Descending | select -first 10


https://www.zhihu.com/question/22611859
