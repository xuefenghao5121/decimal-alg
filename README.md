# BigDecimal Algorithmic Optimization (JDK 25+)

纯 Java 算法级优化 BigDecimal，适用于 JDK 25+。

## 性能成果

| 操作 | FastDecimalV2 | JDK BigDecimal | 加速比 |
|------|---------------|----------------|--------|
| DIVIDE | 2.60 ns/op | ~25 ns/op | **9.6x** |
| MULTIPLY | 2.52 ns/op | ~20 ns/op | **7.9x** |
| SETSCALE | 2.52 ns/op | ~35 ns/op | **13.9x** |

## 核心设计

使用 `long` 存储固定精度数值，避免 BigDecimal 对象分配：

```java
// scale=2 时，12345 表示 123.45
FixedScaleCalculator calc = new FixedScaleCalculator(2);
long price = 12345;  // 123.45 元
long result = calc.divide(price, 100L);  // 直接 long 运算
```

## 快速开始

```java
import com.kunpeng.math.FastDecimalV2;
import com.kunpeng.math.FastDecimalV2.FixedScaleCalculator;

// 创建计算器（货币精度，2位小数）
FixedScaleCalculator calc = FastDecimalV2.createCurrencyCalculator();

// 输入转换
long price = calc.toLong(new BigDecimal("123.45"));  // 12345

// 核心运算（全程使用 long，3-10x 加速）
long result = calc.divide(price, 100L);
result = calc.multiply(result, 2L);
result = calc.setScale(result, 0);

// 输出转换
BigDecimal output = calc.toDecimal(result);
```

## 适用场景

- ✓ 金融计算（固定2位小数）
- ✓ 统计计算（百分比，4位小数）
- ✓ 批量数据处理
- ✗ 高精度计算（>18位）
- ✗ 动态scale场景

## 编译和测试

```bash
# 编译
javac -d build src/main/java/com/kunpeng/math/FastDecimalV2.java

# 运行测试
javac -cp build FastDecimalV2Guide.java
java -cp build:build FastDecimalV2Guide
```

## JDK 版本要求

- JDK 25+
- 支持 JDK 21+（移除 `record` 相关语法）

## License

MIT License
