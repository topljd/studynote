## pydub 中文文档

#### 打开一个音频文件（wav文件）

```python
from pydub import AudioSegment
song = AudioSegment.from_wav('never_gonna_give_you_up.wav')
```

打开一个MP3文件:

```python
song = AudioSegment.from_mp3('never_gonna_you_up.mp3')
```

打开一个OGG或FLV或其他的什么[FFMPEG支持的文件](http://www.ffmpeg.org/general.html#File-Formats)：

```python
ogg_version = AudioSegment.from_ogg("never_gonna_give_you_up.ogg")
flv_version = AudioSegment.from_flv("never_gonna_give_you_up.flv")
mp4_version = AudioSegment.from_file("never_gonna_give_you_up.mp4", "mp4")
wma_version = AudioSegment.from_file("never_gonna_give_you_up.wma", "wma")
aac_version = AudioSegment.from_file("never_gonna_give_you_up.aiff", "aac")
```

#### 对音频段切片

切片操作(从已导入的音频段中提取某一个片段)

```python
#pydub做任何操作的时间尺度都是毫秒
ten_seconds = 10 *1000
first_10_seconds = song[:ten_seconds]#开头10秒
last_5_seconds = song[-5000:]#末尾5秒
```

#### 让开头更响和让结束更弱

使开头十秒的声音变得更响并结束的五秒声音变弱：

```python
#声音增益6db
beginning = first_10_seconds+6
#声音减弱3db
end = last_5_seconds -3
```

#### 连接音频段

连接两个音频（把一个文件接在另一个后面)

```python
without_the_middle = beginning + end
```

#### 音频段长度

音频段有多长呢？

```python
without_the_middle.duration_seconds == 15.0
```

音频段是不可变的

```python
#音频不可以被修改
backwards = song.reverse()
```

#### 交叉淡化

（再一次强调，benginning 和 end 都是不可变的）

```python
#1.5秒的淡入淡出
with_style = beginning.append(end,crossfade=1500)
```

#### 重复

```python
#将前端重复两遍
do_it_over = with_style *2
```

#### 淡化

淡化（注意一下，你可以把许多运算符连成一串使用，因为运算符都会返回一个AudioSegment对象)

```python
#2秒淡入，3秒淡出
awesome = do_it_over.fade_in(2000).fade_out(3000)
```

#### 保存结果

保存编辑的结果(再说一下，支持所有ffmpeg支持的格式)

```python
awesome.export('mashup.mp3',format='mp3')
```

保存带有标签的结果（元数据)

```python
awesome.export("mashup.mp3", format="mp3", tags={'artist': 'Various artists', 'album': 'Best of 2011', 'comments': 'This album is awesome!'})
```

你也可以通过指定任意ffmpeg支持的比特率来导出你的结果

```python
awesome.export("mashup.mp3", format="mp3", bitrate="192k")
```

更多其他的ffmpeg所支持的参数可以通过给’parameters’参数传递一个列表来实现，列表中第一个应该是选项，而第二个是对应的参数。
特别注意一下，这些参数没有得到确认，支持的参数可能会受限于你所使用的特定的 ffmpeg / avlib 构建

```python
# 使用预设MP3质量0(相当于lame -V0)
# ——lame是个MP3编码器，-V设置的是VBR压缩级别,品质从0到9依次递减（译者注）
awesome.export("mashup.mp3", format="mp3", parameters=["-q:a", "0"])

# 混合到双声道并设置输出音量百分比（放大为原来的150%）
awesome.export("mashup.mp3", format="mp3", parameters=["-ac", "2", "-vol", "150"])
```

