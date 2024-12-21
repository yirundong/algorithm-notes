# BitMap

## 位图

位图是一种表,是为了查询某个元素的二元特性的数据结构,区别于其他表,位图空间占用特别小,因为它使用一个二进制位代表一种状态,通常使用位图实现空间压缩

## 代码

```java
public class BitMap{
    private long[] bits;
    public BitMap(int size){
        bits = new long[(size + 64) >> 6];
    }
    public void turnOn(int index){
        bits[index >> 6] |= (1L << (index & 63));
    }
    public void turnOff(int index){
        bits[index >> 6] &= ~(1L << (index & 63));
    }
    public boolean contains(int index){
        return (bits[index >> 6] & (1L << (index & 63))) != 0;
    }
}
```

## 代码API手册

- `BitMap m = new BitMap(size); //一共有size个元素的状态要用位图存储`
- `turnOn(index); //index是元素的索引,这个方法用来将代表该位置的二进制位置为1`
- `turnOff(index); //置0`
- `contains(index); //看index元素的状态是否为1`