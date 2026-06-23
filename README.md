# BigDecimal 算法级优化 (JDK 21+)

纯 Java 算法级优化 BigDecimal，适用于固定精度场景 (scale 0-18)。

## Patch 版本

- JDK 25+: `fastdecimal-jdk25.patch`
- JDK 21+: `fastdecimal-jdk21.patch`

## 性能成果

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
long price = 12345;  // 123.45 元
long result = calc.divide(price, 100L);  // 直接 long 运算
```

## 适用场景

- ✓ 固定精度计算（scale 0-18）
- ✓ 金融计算（货币精度，2位小数）
- ✓ 统计计算（百分比，4位小数）
- ✓ 批量数据处理
- ✗ 高精度计算（>18位）
- ✗ 动态 scale 场景

## 快速开始

```bash
# 1. 复制 FastDecimal.java 到你的项目
cp FastDecimal.java 你的项目路径/

# 2. 编译
javac FastDecimal.java

# 3. 使用
```

```java
import com.kunpeng.math.FastDecimal;
import com.kunpeng.math.FastDecimal.FixedScaleCalculator;

// 创建计算器（scale 0-18）
FixedScaleCalculator calc = FastDecimal.createCalculator(2);

// 输入转换
long value = calc.toLong(new BigDecimal("123.45"));  // 12345

// 核心运算（7-14x 加速）
long result = calc.divide(value, 100L);
result = calc.multiply(result, 2L);
result = calc.setScale(result, 0);

// 输出转换
BigDecimal output = calc.toDecimal(result);
```

## 系统要求

- JDK 21+ (fastdecimal-jdk21.patch)
- JDK 25+ (fastdecimal-jdk25.patch)

## License

MIT License

---

## 与 decimal4j 对比

| 特性 | FastDecimal | decimal4j | BigDecimal |
|------|-------------|-----------|-----------|
| 性能 | **最优** (9-14x) | 较慢 (0.4-0.7x) | 基准 (1x) |
| 精度支持 | 0-18 位 | 0-18 位 | 任意 |
| API 复杂度 | 简单 | 复杂 | 简单 |
| 对象分配 | 最小 | 较多 | 最多 |

**结论**: FastDecimal 在固定精度场景下性能最优。
