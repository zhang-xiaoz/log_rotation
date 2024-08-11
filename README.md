# log_rotation

> ​	log_rotation是一个用GO编写的文件分割处理接口，实现了一个自动日志轮转的功能，能够根据设定的文件大小、文件数量、保存时间等条件，对日志文件进行备份、压缩和删除，确保日志管理高效且不会占用过多存储空间

# 开始

**导入依赖**

```golang
import "github.com/zhang-xiaoz/log_rotation"
```

**或者使用 go get**

```golang
go get -u github.com/zhang-xiaoz/log_rotation
```

**关键字段介绍**

```golang
type Logger struct {

  //文件名

  Filename string

  //每个文件最大大小

  MaxSize int

  //文件最长保存时间//单位天

  MaxSave int

  //文件保存最大数量

  MaxNumbers int

  //文件是否压缩

  Compress bool

}
```

**简单样例**

```golang
package main

import (

  "cc/log_rotation"

  "go.uber.org/zap"

  "go.uber.org/zap/zapcore"

)

func main() {

  InitLogger()

  SugarLogger.Error("aaa")

  for {

​    SugarLogger.Error("aaa")

  }

}

var SugarLogger *zap.SugaredLogger

func InitLogger() {

  encoder := getEncoder()

  writeSyncer := getLogWriter()

  core := zapcore.NewCore(encoder, writeSyncer, zapcore.DebugLevel)

  // zap.AddCaller()  添加将调用函数信息记录到日志中的功能。

  logger := zap.New(core, zap.AddCaller())

  SugarLogger = logger.Sugar()

}

func getEncoder() zapcore.Encoder {

  encoderConfig := zap.NewProductionEncoderConfig()

  // 修改时间编码器

  encoderConfig.EncodeTime = zapcore.ISO8601TimeEncoder

  // 在日志文件中使用大写字母记录日志级别

  encoderConfig.EncodeLevel = zapcore.CapitalLevelEncoder

  // NewConsoleEncoder 打印更符合人们观察的方式

  return zapcore.NewConsoleEncoder(encoderConfig)

}

func getLogWriter() zapcore.WriteSyncer {

  log_rotation_logger := &log_rotation.Logger{

​    Filename:  "./main.exe-log_rotation.log",

​    MaxSize:   50,

​    MaxNumbers: 3,

​    Compress:  true,

​    MaxSave:   1,

  }

  return zapcore.AddSync(log_rotation_logger)

}
```

