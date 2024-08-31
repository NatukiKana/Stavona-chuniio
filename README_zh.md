
# Stavona Chuniio

Updated on 2024/8/31

## 功能
- 基本功能
- 支持热插拔，没有设备时也不会导致timeout
- 支持使用原生Ground Slider配合自制Air

## 消费者使用说明
将以下选项写入到segatools.ini（如果已经有了就直接改）

[chuniio]  
path=Stavona.dll

## 开发者使用说明

- 请确定你的设备的Vendor ID是 `0x303A` ，Product ID是 `0x81F7`.
- 如果你的设备只有一个report，LED将无法使用
- 如果需要自定义Product ID，请发个issue联系我

## 通讯
- 本项目使用Raw HID进行设备间通讯

### 设备 -> 游戏

- Raw HID Data
```
    01 00 00 00 00 00 00 00  
    00 00 00 00 00 00 00 00  
    00 00 00 00 00 00 00 00  
    00 00 00 00 00 00 00 00  
    00 00
```
- Struct

```
struct
{
    unsigned char reportID;
    uint8_t touchValue[32];
    uint8_t ir;
    uint8_t state;
};
```
    
- `reportID` 是 0x01，不要更改
- `touchValue` 是从 入力１(touch1) 到 入力３２(touch32)的触摸数据
- `ir` 是6个Air-String的数据，但只使用6个bit.
- `state` 是test，service和coin按键，只使用3个bit

### 游戏 -> 设备
- Raw HID Data
```
    02 00 00 00 00 00 00 00  
    00 00 00 00 00 00 00 00  
    00 00 00 00 00 00 00 00  
    00 00 00 00 00 00 00 00  
    00 00 00 00 00 00 00 00  
    00 00 00 00 00 00 00 00  
    00
```
- Struct
```
struct
{
    unsigned char reportID;
    unsigned char packageID;
    union
    {
        uint8_t sliderled[48];      // packageID 0x00, touch led
        uint8_t splitled[45];       // packageID 0x01, split led
        uint8_t airled[18];         // packageID 0x02, air led
        uint8_t billboardled[60];   // packageID 0x03-0x08, billboard led, now in progress
    };
};
```

- `reportID` 是 0x02, 不要更改
- LED 数据有3个通道, 所以一个LED有三个数据, 就像 'r g b r g b r g b'.
