# FastDecimal - BigDecimal 优化库 (JDK 25+)

## 一键使用

### 方法 1：直接复制（推荐）

```bash
# 1. 复制 FastDecimal.java 到你的项目
cp src/main/java/com/kunpeng/math/FastDecimal.java 你的项目路径/

# 2. 编译
javac 你的项目路径/FastDecimal.java

# 3. 使用
```

### 方法 2：使用 JAR

```bash
# 编译 JAR
javac -d build src/main/java/com/kunpeng/math/FastDecimal.java
jar cf fastdecimal.jar -C build com/

# 添加到你的项目
java -cp fastdecimal.jar:你的应用.jar 你的主类
```

## 快速示例

```java
import com.kunpeng.math.FastDecimal;
import com.kunpeng.math.FastDecimal.FixedScaleCalculator;

// 货币计算（2位小数）
FixedScaleCalculator calc = FastDecimal.createCurrencyCalculator();

// 输入转换
long price = calc.toLong(new BigDecimal("123.45"));  // 12345

// 核心运算（7-14x 加速）
long result = calc.divide(price, 100L);
result = calc.multiply(result, 2L);
result = calc.setScale(result, 0);

// 输出转换
BigDecimal output = calc.toDecimal(result);
```

## 性能对比

| 操作 | FastDecimal | JDK BigDecimal | 加速比 |
|------|-------------|----------------|--------|
| DIVIDE | 2.60 ns | 25 ns | **9.6x** |
| MULTIPLY | 2.52 ns | 20 ns | **7.9x** |
| SETSCALE | 2.52 ns | 35 ns | **13.9x** |

## 系统要求

- JDK 25+
- 或者 JDK 21+（移除 `record` 语法）

## 适用场景

✓ 金融计算（固定2位小数）
✓ 统计计算（百分比，4位小数）  
✓ 批量数据处理
✗ 高精度计算（>18位）
✗ 动态scale场景
