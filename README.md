# BigDecimal Algorithmic Optimization (JDK 25+)

纯 Java 算法级优化 BigDecimal，适用于 JDK 25+。

## 性能

| 操作 | FastDecimal | JDK BigDecimal | 加速比 |
|------|-------------|----------------|--------|
| DIVIDE | 2.60 ns/op | ~25 ns/op | **9.6x** |
| MULTIPLY | 2.52 ns/op | ~20 ns/op | **7.9x** |
| SETSCALE | 2.52 ns/op | ~35 ns/op | **13.9x** |

## 核心设计

使用 `long` 存储固定精度数值，避免 BigDecimal 对象分配：

```java
// scale=2 时，12345 表示 123.45
FixedScaleCalculator calc = new FixedScaleCalculator(2);
long price = 12345;  // 123.45
long result = calc.divide(price, 100L);  // 直接 long 运算
```

## 快速开始

```java
import com.kunpeng.math.FastDecimal;
import com.kunpeng.math.FastDecimal.FixedScaleCalculator;

// 创建计算器（scale 0-18）
FixedScaleCalculator calc = FastDecimal.createCalculator(2);

// 输入转换
long value = calc.toLong(new BigDecimal("123.45"));  // 12345

// 核心运算（全程使用 long，7-14x 加速）
long result = calc.divide(value, 100L);
result = calc.multiply(result, 2L);
result = calc.setScale(result, 0);

// 输出转换
BigDecimal output = calc.toDecimal(result);
```

## 适用场景

- ✓ 固定精度计算（scale 0-18）
- ✓ 批量数据处理
- ✗ 高精度计算（>18位）
- ✗ 动态scale场景

## 系统要求

- JDK 25+
- 或者 JDK 21+（移除 `record` 语法）

## License

MIT License
