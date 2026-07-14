---
title: 时间序列与视频或音频
short: 时间序列与视频或音频
meta_title: 时间序列视频音频标注
---


!!! note 注意
    - 此功能未经官方支持，属于实验性功能，配置方式较为复杂。
    - 要使用音频而非视频，您应将 `.mp4` 文件替换为 mp3/wav/其他浏览器支持的音频格式。
    - 此方法适用于 Label Studio **1.4.1 及更低版本**。

## 此方法已弃用

此方法已完全弃用。

在 Label Studio 1.20 中，我们引入了同步时间序列与视频和音频的新方法。详情请参阅：
https://labelstud.io/templates/timeseries_audio_video


## 第 1 步：托管媒体文件

您需要将 CSV 和 MP4（或其他浏览器支持的视频格式）文件托管在某处。您可以使用 S3 或其他云服务。如果想从本地硬盘托管数据，请使用
[Label Studio 本地存储](http://localhost:4000/guide/storage.html#Local-storage)。

完成此步骤后，您将获得两个 URL：一个用于 CSV，一个用于视频。例如：
```
http://localhost:8080/samples/time-series.csv?time=time_column&values=first_column
http://localhost:8080/static/samples/opossum_snow.mp4
```


## 第 2 步：带有示例任务数据的标注配置

将以下标注配置复制到您的项目中。

```
<View>
  <TimeSeriesLabels name="label" toName="ts">
    <Label value="Run"/>
    <Label value="Walk"/>
  </TimeSeriesLabels>
  <HyperText name="video" value="$video" inline="true"/>
  <TimeSeries name="ts" value="$csv" valueType="url" timeColumn="time_column">
    <Channel column="first_column"/>
  </TimeSeries>
</View>

<!-- {
    "csv": "/samples/time-series.csv?time=time_column&values=first_column",
    "video": "<video src='/static/samples/opossum_snow.mp4' width='100%' controls onloadeddata=\"setTimeout(function(){ts=Htx.annotationStore.selected.names.get('ts');t=ts.data.time_column;v=document.getElementsByTagName('video')[0];w=parseInt(t.length*(5/v.duration));l=t.length-w;ts.updateTR([t[0], t[w]], 1.001);r=$=>
ts.brushRange.map(n=>(+n).toFixed(2));_=r();setInterval($=>r().some((n,i)=>n!==_[i])&&(_=r())&&(v.currentTime=v.duration*(r()[0]-t[0])/(t.slice(-1)[0]-t[0]-(r()[1]-r()[0]))),300); console.log('video is loaded, starting to sync with time series')}, 3000); \" />"
  } -->
```


## 第 3 步：准备并导入任务

!!! note 注意
    这是最关键的一步，因为主要的技巧在于任务数据，它使用了 JavaScript 注入。

将以下代码保存为 `import.json`，然后将此文件导入到 LS 中。
```
{
    "csv": "/samples/time-series.csv?time=time_column&values=first_column",
    "video": "<video src='/static/samples/opossum_snow.mp4' width='100%' controls onloadeddata=\"setTimeout(function(){ts=Htx.annotationStore.selected.names.get('ts');t=ts.data.time_column;v=document.getElementsByTagName('video')[0];w=parseInt(t.length*(5/v.duration));l=t.length-w;ts.updateTR([t[0], t[w]], 1.001);r=$=>
ts.brushRange.map(n=>(+n).toFixed(2));_=r();setInterval($=>r().some((n,i)=>n!==_[i])&&(_=r())&&(v.currentTime=v.duration*(r()[0]-t[0])/(t.slice(-1)[0]-t[0]-(r()[1]-r()[0]))),300); console.log('video is loaded, starting to sync with time series')}, 3000); \" />"
}
```


## 第 4 步：查看已导入的任务

1. 前往数据管理器，点击任务行。
2. 务必等待约 3 秒（取决于视频大小），直到视频加载完成。
3. 拖动时间序列概览通道上的滑块，您的视频应与时间序列同步。

!!! note 注意
    - 此技巧假设时间序列的长度等于视频的长度，因此时间序列和视频之间的滚动是按比例工作的。
    - 同步仅单向工作：从时间序列到视频，您只能使用时间序列滚动条来控制时间位置。


## 视频教程

<iframe width="100%" height="450vh" src="https://www.youtube.com/embed/fzY1DNPxkdw" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
