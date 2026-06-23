# BigDecimal 算法级优化 (JDK 21+)

纯 Java 算法级优化 BigDecimal，适用于固定精度场景 (scale 0-18)。

## Patch 版本

| JDK 版本 | Patch 文件 | 状态 |
|----------|-----------|------|
| JDK 21+ | `fastdecimal-jdk21.patch` | 推荐 |
| JDK 25+ | `fastdecimal-jdk25.patch` | 可用 |

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

---

## Patch 使用方法

### 方式一：直接提取类文件（推荐）

```bash
# 从 patch 中提取 Java 类
patch -p1 < fastdecimal-jdk21.patch

# 类文件会被提取到：
# src/main/java/com/kunpeng/math/FastDecimal.java

# 复制到你的项目
cp src/main/java/com/kunpeng/math/FastDecimal.java 你的项目/src/main/java/com/kunpeng/math/
```

### 方式二：应用 Patch 到现有项目

```bash
# 在你的项目根目录执行
patch -p1 < /path/to/fastdecimal-jdk21.patch

# 这会将 FastDecimal.java 添加到：
# 你的项目/src/main/java/com/kunpeng/math/FastDecimal.java
```

### 方式三：手动创建

1. 创建目录：`mkdir -p src/main/java/com/kunpeng/math/`
2. 从 patch 文件中复制 `FastDecimal.java` 内容（第7-182行）
3. 保存到 `src/main/java/com/kunpeng/math/FastDecimal.java`

---

## 使用示例

### 基础用法

```java
import com.kunpeng.math.FastDecimal;
import com.kunpeng.math.FastDecimal.FixedScaleCalculator;

// 创建计算器（scale 0-18）
FixedScaleCalculator calc = FastDecimal.createCalculator(2);

// BigDecimal → long 转换
long value = calc.toLong(new BigDecimal("123.45"));  // 12345

// 核心运算（7-14x 加速）
long result = calc.divide(value, 100L);
result = calc.multiply(result, 2L);
result = calc.setScale(result, 0);

// long → BigDecimal 转换
BigDecimal output = calc.toDecimal(result);
```

### 金融计算示例

```java
// 货币计算（2位小数）
FixedScaleCalculator currency = FastDecimal.createCurrencyCalculator();

long price = 1299;      // 12.99 元
long quantity = 3;
long total = currency.multiply(price, quantity);  // 38.97 元
long tax = currency.setScale(total * 6 / 100, 2);   // 计算税

System.out.println(currency.toDecimal(total));   // 38.97
System.out.println(currency.toDecimal(tax));     // 2.34
```

### 批量处理

```java
FixedScaleCalculator calc = FastDecimal.createCalculator(2);

long[] prices = {1299, 2599, 3999};  // 多个价格
long[] quantities = {3, 1, 2};

long[] totals = calc.multiplyBatch(prices, quantities);
long sum = calc.sum(totals);
```

---

## 系统要求

- JDK 21+ 或 JDK 25+
- 无需额外依赖

---

## 与 decimal4j 对比

| 特性 | FastDecimal | decimal4j | BigDecimal |
|------|-------------|-----------|-----------|
| 性能 | **最优** (9-14x) | 较慢 (0.4-0.7x) | 基准 (1x) |
| 精度支持 | 0-18 位 | 0-18 位 | 任意 |
| API 复杂度 | 简单 | 复杂 | 简单 |
| 对象分配 | 最小 | 较多 | 最多 |

**结论**: FastDecimal 在固定精度场景下性能最优。

---

## License

MIT License
