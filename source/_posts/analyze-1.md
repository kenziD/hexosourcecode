title: Pixhawk Source Code Analyze(1)
date: 2015-11-30 10:40:05
tags: pixhawk
---
## Makefile

```
# top level makefile to build SITL for primary vehicle targets. 
# Useful for static analysis tools

all: sitl

sitl: TARGET=sitl
sitl: plane copter rover antennatracker

linux: TARGET=linux
linux: plane copter rover antennatracker

clean: TARGET=clean
clean: plane copter rover antennatracker

# 声明后面的这些命令是伪目标
.PHONY: plane copter rover antennatracker

plane:
	$(MAKE) -C ArduPlane $(TARGET)

copter:
	$(MAKE) -C ArduCopter $(TARGET)

rover:
	$(MAKE) -C APMrover2 $(TARGET)

antennatracker:
	$(MAKE) -C AntennaTracker $(TARGET)

```

参考:[Make 命令教程](http://www.ruanyifeng.com/blog/2015/02/make.html)