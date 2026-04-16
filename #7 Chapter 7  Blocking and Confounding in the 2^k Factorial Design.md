# 📚 Comprehensive Notes: Chapter 7 - Blocking and Confounding in the 2^k Factorial Design

_Written for beginners - All concepts explained step-by-step with examples, tables, and R code_

---

## 🎯 Part 1: What is Blocking? (The Big Picture)

### 1.1 Why Do We Need Blocking?

**Problem**: In real experiments, we often cannot run all treatments under identical conditions.

**Examples of "nuisance factors"** (things we can't control but that affect results):

|Nuisance Factor|Example|
|---|---|
|**Time**|Morning vs. afternoon runs; different days|
|**People**|Different operators, technicians, or subjects|
|**Materials**|Different batches of raw materials|
|**Equipment**|Different machines or instruments|

**Solution**: **Blocking** = Group similar experimental units together and randomize treatments _within_ each group.

> 💡 **Key Idea**: Blocking removes variability due to nuisance factors, making it easier to detect true treatment effects.

---

### 1.2 Basic Terminology

|Term|Definition|Simple Analogy|
|---|---|---|
|**Block**|A group of experimental units that are similar to each other|Like testing cake recipes in the same oven on the same day|
|**Treatment**|A specific combination of factor levels we want to test|A specific recipe with specific temperature and sugar amount|
|**Replicate**|Running the same treatment combination more than once|Baking the same recipe 3 times to check consistency|
|**Randomization**|Assigning treatments to units in random order|Shuffling recipe cards before baking|
|**Confounding**|When we cannot separate the effect of a treatment from the effect of a block|If we always test Recipe A in Oven 1 and Recipe B in Oven 2, we can't tell if differences are due to recipe or oven|

#### Confounding

To say an interaction is **confounded** with blocks means that the effect of that interaction and the effect of the blocks are "tangled" together so that they cannot be separated mathematically.

In simple terms: if you see a large difference in your results between Block 1 and Block 2, you have no way of knowing if that difference was caused by the **Blocks** (e.g., Day 1 vs. Day 2) or by the **Interaction** (e.g., ABCD).

When we **confound** an effect with blocks, we **deliberately arrange the experiment** so that we **cannot tell the difference** between:

- The effect of that treatment interaction, AND
    
- The difference between blocks
    

#### Why Do We Confound?

**The Problem:** Sometimes we **cannot run all treatments** under the same conditions. For example:

- Only 2 runs per day (but need 4 total)
    
- Only 4 runs per batch (but need 8 total)
    
- Limited materials, time, or equipment
    

**The Solution:** We split the experiment into **blocks**, but this means we must **sacrifice** the ability to estimate certain effects. We choose to confound effects we think are **unimportant** (usually high-order interactions).

---

## 🔬 Part 2: Example 1 - Radar Detection Experiment (Randomized Complete Block Design)

### 2.1 Research Question

An engineer wants to improve radar target detection. Two factors are studied:

- **Factor A: Ground Clutter** (background noise on radar screen)
    
    - Levels: Low, Medium, High
        
- **Factor B: Filter Type** (screen filter)
    
    - Levels: Type 1, Type 2
        

**Response Variable**: Signal intensity at which operator detects target (lower = better detection)

### 2.2 The Blocking Factor

- **Problem**: Operators differ in skill. We can't test all combinations with the same operator in one session.
    
- **Solution**: Use **4 different operators as blocks**. Each operator tests all 6 treatment combinations (3 clutter × 2 filter).
    

### 2.3 Experimental Design: Randomized Complete Block Design (RCBD)

For each of 4 operators (blocks):  
  → Randomly order the 6 treatment combinations  
  → Run all 6 tests with that operator

### 2.4 Data Table (From Lecture)

![image-20260404143534192](file:///D:/Obsidian/repo/Ualberta/STAT-368/assets/image-20260404143534192.png?lastModify=1776320678)

|Operator (Block)|Filter Type|Ground Clutter|Response (Detection Intensity)|
|---|---|---|---|
|**1**|1|Low|90|
|**1**|2|Low|86|
|**1**|1|Medium|102|
|**1**|2|Medium|87|
|**1**|1|High|114|
|**1**|2|High|93|
|**2**|1|Low|96|
|**2**|2|Low|84|
|**2**|1|Medium|106|
|**2**|2|Medium|90|
|**2**|1|High|112|
|**2**|2|High|91|
|**3**|1|Low|100|
|**3**|2|Low|92|
|**3**|1|Medium|105|
|**3**|2|Medium|97|
|**3**|1|High|108|
|**3**|2|High|95|
|**4**|1|Low|92|
|**4**|2|Low|81|
|**4**|1|Medium|96|
|**4**|2|Medium|80|
|**4**|1|High|98|
|**4**|2|High|83|

**R Code to Enter Data**:

# Response variable (in order as listed above)  
y <- c(90, 102, 114, 86, 87, 93,    # Operator 1  
       96, 106, 112, 84, 90, 91,    # Operator 2  
       100, 105, 108, 92, 97, 95,   # Operator 3  
       92, 96, 98, 81, 80, 83)      # Operator 4  
  
# Create factor variables  
Block <- factor(rep(c(1,2,3,4), each = 6))  # 4 operators  
Filter <- factor(rep(c(1,2), each = 3, times = 4))  # Filter types  
Clutter <- factor(rep(c("Low","Medium","High"), times = 8))  # Clutter levels

---

### 2.5 Statistical Model for RCBD

$$  
y_{ijk} = \mu + \alpha_i + \beta_j + \gamma_k + (\alpha\beta)_{ij} + \varepsilon_{ijk}  
$$

  

Where:

- y_{ijk} = observed response
    
- \mu = overall mean
    
- \alpha_i = effect of i-th clutter level (i = 1,2,3)
    
- \beta_j = effect of j-th filter type (j = 1,2)
    
- \gamma_k = effect of k-th operator/block (k = 1,2,3,4)
    
- (\alpha\beta)_{ij} = interaction between clutter and filter
    
- \varepsilon_{ijk} = random error (assumed normal, mean 0, constant variance)
    

---

### 2.6 Full Model ANOVA (All Possible Effects)

**R Code**:

model_full <- lm(y ~ (Clutter + Filter + Block)^3)  # All main effects + all interactions  
anova(model_full)

**ANOVA Table** (From Lecture):

|Source|df|Sum Sq|Mean Sq|F value|Pr(>F)|
|---|---|---|---|---|---|
|Clutter|2|335.58|167.79|—|—|
|Filter|1|1066.67|1066.67|—|—|
|Operator (Block)|3|402.17|134.06|—|—|
|Clutter:Filter|2|77.08|38.54|—|—|
|Clutter:Operator|6|99.08|16.51|—|—|
|Filter:Operator|3|34.33|11.44|—|—|
|Clutter:Filter:Operator|6|32.92|5.49|—|—|
|**Residuals**|**0**|**0.00**|—|—|—|

⚠️ **Warning**: "ANOVA F-tests on an essentially perfect fit are unreliable"

**Why?** We have:

- Total observations: n = 24
    
- Parameters in full model: 1 + 2 + 1 + 3 + 2 + 6 + 3 + 6 = 24
    
- Degrees of freedom for error = 24 - 24 = 0 → **No degrees of freedom left to estimate error!**
    

---

### 2.7 Model Simplification: Keep Only Important Effects(look at interaction plot)

**Strategy**: Remove non-significant interaction terms to free up degrees of freedom for error estimation.

![image-20260404150626446](file:///D:/Obsidian/repo/Ualberta/STAT-368/assets/image-20260404150626446.png?lastModify=1776320678)

**Step 1**: Fit model with main effects + Clutter:Filter interaction only

model_reduced <- lm(y ~ Clutter + Filter + Clutter*Filter + Block)  
anova(model_reduced)

**Reduced ANOVA Table**:

|Source|df|Sum Sq|Mean Sq|F value|Pr(>F)|Significance|
|---|---|---|---|---|---|---|
|Clutter|2|335.58|167.79|15.13|0.00025|***|
|Filter|1|1066.67|1066.67|96.19|6.45e-08|***|
|Operator (Block)|3|402.17|134.06|12.09|0.00028|***|
|Clutter:Filter|2|77.08|38.54|3.48|0.0575|.|
|**Residuals**|**15**|**166.33**|**11.09**|—|—|—|

✅ **Interpretation**:

- **Filter Type** is HIGHLY significant (p < 0.001): Filter 2 gives much better detection
    
- **Ground Clutter** is HIGHLY significant: Higher clutter makes detection harder
    
- **Operator** is significant: Operators differ in skill (this is why blocking was important!)
    
- **Clutter:Filter interaction** is marginally significant (p = 0.0575): The effect of filter may depend slightly on clutter level
    

---

### 2.8 Interaction Plots (Visualizing Effects)

**R Code**:

par(mfrow = c(2,2))  # 4 plots in one window  
  
# Plot 1: Filter effect at different clutter levels  
interaction.plot(Clutter, Filter, y,   
                 type = "b", pch = c(18,24),  
                 leg.bty = "o", main = "Filter × Clutter")  
  
# Plot 2: Clutter effect at different filter levels    
interaction.plot(Filter, Clutter, y,  
                 type = "b", pch = c(24,18),  
                 leg.bty = "o", main = "Clutter × Filter")  
  
# Plot 3: Filter effect by operator  
interaction.plot(Filter, Block, y, main = "Filter × Operator")  
  
# Plot 4: Clutter effect by operator  
interaction.plot(Clutter, Block, y, main = "Clutter × Operator")

**How to Read Interaction Plots**:

- **Parallel lines** = No interaction (effects are additive)
    
- **Crossing/non-parallel lines** = Interaction present (effect of one factor depends on the other)
    

✅ **From plots**: Filter 2 consistently gives lower detection intensity (better) across all clutter levels, but the advantage is slightly larger at high clutter.

![image-20260404144201295](file:///D:/Obsidian/repo/Ualberta/STAT-368/assets/image-20260404144201295.png?lastModify=1776320678)

1. Filter vs. Clutter Interaction
    

- **Observation:** The lines for High, Medium, and Low clutter are **not parallel**.
    
- **Analysis:** This indicates a significant **interaction between the filter type and ground clutter**. The intensity level needed to detect a target changes differently for each filter depending on the clutter level. Specifically, the gap between Filter 1 and Filter 2 is wider at High clutter than at Low clutter.
    
- **Statistical Confirmation:** The ANOVA table on Page 7 confirms this with a significant p-value of **0.026796**.
    

2. Clutter vs. Filter Interaction
    

- **Observation:** This is the inverse of the first plot. The lines for Filter 1 and Filter 2 show different slopes as clutter increases.
    
- **Analysis:** It reinforces that the "Filter" effect is dependent on the "Clutter" level. Filter 1 consistently requires higher intensity than Filter 2, but this difference is magnified as clutter increases.
    

3. Filter vs. Operator Interaction
    

- **Observation:** The lines representing the four operators are **roughly parallel**.
    
- **Analysis:** This suggests **no significant interaction**. All operators respond to the change in filter type in a similar way. The vertical distance between the lines simply shows that some operators are naturally better (more sensitive) than others.
    
- **Statistical Confirmation:** The ANOVA table on Page 7 shows a non-significant p-value of **0.2035**.
    

4. Clutter vs. Operator Interaction
    

- **Observation:** The lines for the operators remain **parallel** as clutter levels increase from Low to High.
    
- **Analysis:** There is **no significant interaction**. Increasing ground clutter affects all operators' detection abilities equally.
    
- **Statistical Confirmation:** The ANOVA table on Page 7 shows a non-significant p-value of **0.1028**.
    

---

### 2.9 Half-Normal Plot: Identifying Significant Effects

**Purpose**: When you have many potential effects, this plot helps identify which ones are "large" (likely real) vs. "small" (likely noise).

**R Code**:

# Fit model with all 2-way interactions  
model <- lm(y ~ (Clutter + Filter + Block)^2)  
  
# Extract effect estimates (exclude intercept)  
model.effects <- model$effects[2:8]  # Effects for Clutter, Filter, Block, and interactions  
  
# Take absolute values and create half-normal plot  
abs.effects <- abs(model.effects)  
qq <- qqnorm(abs.effects, type = "n")  # Create empty plot  
text(qq$x, qq$y, labels = names(abs.effects))  # Add effect names  
abline(0, 1, col = "red")  # Reference line

**Interpretation**:

- Points that fall **far above the red line** are likely significant effects
    
- Points **near the line** are consistent with random noise
    

✅ **Result**: Filter, Clutter, and Block effects stand out → these are the important factors.

![image-20260404145146157](file:///D:/Obsidian/repo/Ualberta/STAT-368/assets/image-20260404145146157.png?lastModify=1776320678)

**The Error Line:** Points that fall along the straight diagonal line starting from the bottom-left represent **insignificant effects**. These are essentially random noise. This includes all the **Operator** effects (O2, O3, O4) and most higher-order interactions.

##### Key Observations:

1. **Significant Effects** (deviate from the line):
    
    - **F2** (Filter 2) - largest positive effect (~30)
        
    - **O2** (Operator 2) - negative effect (~5)
        
2. **Moderate Effects**:
    
    - **CLow** and **O3** show moderate positive effects (~15-16)
        
    - **O4** shows a smaller effect (~11)
        
3. **Negligible Effects**:
    
    - **CMedium:CLow:F2** (three-way interaction) appears near zero
        

##### Interpretation:

- **Filter type (F2)** has the strongest effect on detection ability
    
- **Operator differences (O2, O3, O4)** are notable, justifying the use of blocking
    
- **Ground clutter (CLow)** has a moderate effect
    
- The three-way interaction is negligible
    

---

### 2.10 Residual Diagnostics: Checking Model Assumptions

**ANOVA assumptions**:

1. Residuals are normally distributed
    
2. Residuals have constant variance (homoscedasticity)
    
3. Residuals are independent
    

**R Code for Diagnostic Plots**:

par(mfrow = c(3,2))  # 6 plots  
​  
# 1. Normal Q-Q plot  
qqnorm(model_reduced$resid, col = "blue")  
qqline(model_reduced$resid, col = "red")  
​  
# 2. Residuals vs. Fitted values (check constant variance)  
plot(model_reduced$fitted.values, model_reduced$resid, col = "blue")  
abline(h = 0, col = "red")  
​  
# 3-5. Residuals vs. each factor (check for patterns)  
plot(Clutter, model_reduced$resid, col = "blue")  
abline(h = 0, col = "red")  
​  
plot(Filter, model_reduced$resid, col = "blue")   
abline(h = 0, col = "red")  
​  
plot(Block, model_reduced$resid, col = "blue")  
abline(h = 0, col = "red")

✅ **What to look for**:

- Q-Q plot: Points should follow the red line
    
- Residual vs. Fitted: Random scatter around horizontal line (no funnel shape)
    
- Residual vs. Factor: No systematic patterns
    

![image-20260404150731318](file:///D:/Obsidian/repo/Ualberta/STAT-368/assets/image-20260404150731318.png?lastModify=1776320678)

#### **1. Normal Q-Q Plot**

- **Observation:** The points follow the straight red diagonal line quite closely.
    
- **Analysis:** This indicates that the **residuals are normally distributed**. The model's assumption of normality is satisfied, making the P-values in the ANOVA table reliable.
    

#### **2. Residuals vs. Fitted Values**

- **Observation:** The points are randomly scattered around the red horizontal line (y=0) with no distinct patterns (like a funnel or a curve).
    
- **Analysis:** This confirms **constant variance**. The error is consistent regardless of the predicted detection intensity.
    

#### **3. Residuals vs. Clutter**

- **Observation:** The vertical spread of residuals is similar for all three clutter levels (1.0, 2.0, 3.0).
    
- **Analysis:** This confirms that the "Clutter" factor does not introduce non-constant variance; the model predicts accurately across all clutter conditions.
    

#### **4. Residuals vs. Filter**

- **Observation:** The spread for Filter 1 (1.0) and Filter 2 (2.0) is roughly equal and centered at zero.
    
- **Analysis:** This shows the model is equally effective for both filter types, with no bias toward one or the other.
    

#### **5. Residuals vs. Operator**

- **Observation:** The residuals are distributed evenly across the four operators (1.0 through 4.0).
    
- **Analysis:** This confirms that **Blocking was effective**. Using operators as blocks successfully accounted for their individual differences without leaving behind patterned errors.
    

---

## 🧩 Part 3: Latin Square Design (Two Blocking Factors)

### 3.1 When Do We Need Latin Square?

**Scenario**: Two nuisance factors that we want to control simultaneously.

**Example from Lecture**:

- Original radar experiment had **Operator** as blocking factor
    
- Now suppose we also have **Day** as a second restriction (only 6 runs can be made per day due to setup time)
    
- We have 6 treatment combinations (3 clutter × 2 filter)
    
- We can run exactly 6 runs per day → perfect for a **6×6 Latin Square**
    

### 3.2 What is a Latin Square?

A Latin Square is a design where:

- We have p treatments, p rows, and p columns
    
- Each treatment appears **exactly once** in each row and each column
    
- Rows and columns represent two blocking factors
    

**Visual Example** (6×6 Latin Square):

        Operators →  
      1   2   3   4   5   6  
    ┌─────────────────────┐  
Day 1 │ A   B   C   D   E   F │  
Day 2 │ C   A   B   F   D   E │    
Day 3 │ B   E   F   A   C   D │  
Day 4 │ E   D   A   B   F   C │  
Day 5 │ F   C   D   E   A   B │  
Day 6 │ D   F   E   C   B   A │  
    └─────────────────────┘

Where A, B, C, D, E, F represent the 6 treatment combinations.

### 3.3 Treatment Coding for Latin Square

|Label|Filter Type (f)|Ground Clutter (g)|Meaning|
|---|---|---|---|
|**A**|f_1|g_1 (Low)|Filter 1, Low clutter|
|**B**|f_1|g_2 (Medium)|Filter 1, Medium clutter|
|**C**|f_1|g_3 (High)|Filter 1, High clutter|
|**D**|f_2|g_1 (Low)|Filter 2, Low clutter|
|**E**|f_2|g_2 (Medium)|Filter 2, Medium clutter|
|**F**|f_2|g_3 (High)|Filter 2, High clutter|

### 3.4 Data Table for Latin Square Design

|Day|Operator 1|Operator 2|Operator 3|Operator 4|Operator 5|Operator 6|
|---|---|---|---|---|---|---|
|**1**|A=90|B=106|C=108|D=81|F=90|E=88|
|**2**|C=114|A=96|B=105|F=83|E=86|D=84|
|**3**|B=102|E=90|F=95|A=92|D=85|C=104|
|**4**|E=87|D=84|A=100|B=96|C=110|F=91|
|**5**|F=93|C=112|D=92|E=80|A=90|B=98|
|**6**|D=86|F=91|E=97|C=98|B=100|A=92|

**R Code to Enter Data** (in standard order):

y <- c(90, 114, 102, 87, 93, 86,    # Column 1 (Operator 1)  
       106, 96, 90, 84, 112, 91,    # Column 2 (Operator 2)  
       108, 105, 95, 100, 92, 97,   # Column 3  
       81, 83, 92, 96, 80, 98,      # Column 4  
       90, 86, 85, 110, 90, 100,    # Column 5  
       88, 84, 104, 91, 98, 92)     # Column 6  
  
Day <- factor(rep(1:6, times = 6))  
Operator <- factor(rep(1:6, each = 6))  
  
# Filter and Clutter coded according to treatment labels  
Filter <- factor(c(1,1,1,2,2,2,  1,1,2,2,1,2,  1,1,2,1,2,2,  
                   2,2,1,1,2,1,  2,2,2,1,1,1,  2,2,1,2,1,1))  
  
Clutter <- factor(c(1,3,2,2,3,1,  2,1,2,1,3,3,  3,2,3,1,1,2,  
                    1,3,1,2,2,3,  3,2,1,3,1,2,  2,1,3,3,2,1))

---

### Interaction plots:

# Set up a 3x2 grid for the plots  
par(mfrow=c(3,2))  
  
# Generate the interaction plots  
interaction.plot(Filter, Clutter, y)  
interaction.plot(Clutter, Filter, y)  
interaction.plot(Filter, Operator, y)  
interaction.plot(Clutter, Operator, y)  
interaction.plot(Filter, Day, y)  
interaction.plot(Clutter, Day, y)

![image-20260404151545793](file:///D:/Obsidian/repo/Ualberta/STAT-368/assets/image-20260404151545793.png?lastModify=1776320678)

#### **1. Filter vs. Clutter (Top Left & Right)**

- **Observation:** The lines are clearly **not parallel**; the slopes change significantly between Filter 1 and Filter 2 across different clutter levels.
    
- **Analysis:** There is a **strong interaction** between Filter and Clutter. The effectiveness of a filter depends heavily on the amount of ground clutter present.
    

#### **2. Filter vs. Operator (Middle Left)**

- **Observation:** The lines for the six operators are generally **parallel** and follow the same downward trend from Filter 1 to Filter 2.
    
- **Analysis:** **No significant interaction**. While some operators are overall faster or slower at detection (indicated by the vertical spacing), they all respond to the filter change in the same way.
    

#### **3. Clutter vs. Operator (Middle Right)**

- **Observation:** The lines representing the operators follow a similar upward slope as clutter increases.
    
- **Analysis:** **No significant interaction**. The impact of increasing clutter on target detection is consistent across all six operators.
    

#### **4. Filter vs. Day (Bottom Left)**

- **Observation:** The lines for different days are tightly clustered and nearly parallel.
    
- **Analysis:** **No significant interaction**. The "Day" (the second blocking factor) does not change how the filters perform.
    

#### **5. Clutter vs. Day (Bottom Right)**

- **Observation:** The lines are somewhat messy but generally follow the same trend without major crossings.
    
- **Analysis:** **No significant interaction**. Environmental differences between days do not significantly alter the effect that clutter has on detection.
    

The analysis shows that **only the primary factors (Filter & Clutter) interact**. The lack of interaction with the blocking factors (**Operators** and **Days**) validates the Latin Square design—it successfully isolated the nuisance variables without them interfering with the experimental results

---

### Half-Normal Plot

# Define factors  
C = Clutter  
F = Filter  
D = Day  
O = Operator  
  
# Fit the linear model with all interactions up to the 4th order  
model = lm(y ~ (C+F+D+O)^4)  
  
# Display ANOVA table  
anova(model)  
  
# Extract and take the absolute value of the estimated effects (excluding the intercept)  
model$effects  
model.effects = abs(model$effects[-1])  
  
# Create the Normal Q-Q plot of effects  
qq <- qqnorm(model.effects, type="n")  
text(qq$x, qq$y, labels = names(model.effects))

![image-20260404152031131](file:///D:/Obsidian/repo/Ualberta/STAT-368/assets/image-20260404152031131.png?lastModify=1776320678)

This plot is used to distinguish between "active" (significant) factors and "inactive" (noise) factors.

- **Insignificant Effects (The "Noise" Line):** Most of the points cluster along the straight diagonal line starting from the bottom-left. These represent effects that are likely due to random variation, including most high-order interactions and several blocking levels.
    
- **Significant Factors (Outliers):** Points that deviate significantly to the right of the line are considered statistically important:
    
    - **F2 (Filter Type):** Shows the largest absolute effect, making it the most significant factor in the experiment.
        
    - **C3 and C2 (Clutter Levels):** These also stand out as significant outliers, indicating that ground clutter strongly impacts target detection.
        
    - **O3 (Operator 3):** This blocking factor level appears as an outlier, suggesting that specific operator differences are significant enough to justify the use of blocking in the design.
        
    - **C3:F2 (Interaction):** This point suggests a significant interaction between the highest clutter level and the second filter type.
        

### 3.5 ANOVA for Latin Square Design

**R Code**:

# Full model with all 2-way interactions  
model <- lm(y ~ Clutter + Filter + Clutter*Filter + Operator + Day)  
anova(model)

**ANOVA Table**:

|Source|df|Sum Sq|Mean Sq|F value|Pr(>F)|Significance|
|---|---|---|---|---|---|---|
|Clutter|2|571.50|285.75|28.86|1.27e-06|***|
|Filter|1|1469.44|1469.44|148.43|1.04e-10|***|
|Operator|5|428.00|85.60|8.65|0.00017|***|
|Day|5|4.33|0.87|0.09|0.993|(ns)|
|Clutter:Filter|2|126.72|63.36|6.40|0.0071|**|
|**Residuals**|**20**|**198.00**|**9.90**|—|—|—|

✅ **Key Findings**:

- **Filter** and **Clutter** are highly significant (as before)
    
- **Operator** is significant → blocking by operator was necessary
    
- **Day** is NOT significant → day-to-day variation was small
    
- **Clutter:Filter interaction** is significant → effect of filter depends on clutter level
    

### Assumption Test

# Set up the plotting grid (3 rows, 2 columns)  
par(mfrow=c(3,2))  
  
# 1. Normal Q-Q Plot  
qqnorm(model$resid, col="blue")  
qqline(model$resid, col="red")  
  
# 2. Residuals vs. Fitted Values  
plot(model$fitted, model$resid, col="blue", xlab="model$fitted", ylab="model$resid")  
abline(h=0, col="red")  
  
# 3. Residuals vs. Clutter  
plot(as.numeric(Clutter), model$resid, col="blue", xlab="c(Clutter)", ylab="model$resid")  
abline(h=0, col="red")  
  
# 4. Residuals vs. Filter  
plot(as.numeric(Filter), model$resid, col="blue", xlab="c(Filter)", ylab="model$resid")  
abline(h=0, col="red")  
  
# 5. Residuals vs. Operator  
plot(as.numeric(Operator), model$resid, col="blue", xlab="c(Operator)", ylab="model$resid")  
abline(h=0, col="red")  
  
# 6. Residuals vs. Day  
plot(as.numeric(Day), model$resid, col="blue", xlab="c(Day)", ylab="model$resid")  
abline(h=0, col="red")

![image-20260404152405141](file:///D:/Obsidian/repo/Ualberta/STAT-368/assets/image-20260404152405141.png?lastModify=1776320678)

- **Normal Q-Q Plot:** The residuals fall closely along the straight diagonal line. This indicates that the **normality assumption** is satisfied.
    
- **Residuals vs. Fitted Values:** The points are randomly dispersed around the horizontal zero line with no obvious patterns (like a "mega-phone" shape). This confirms **constant variance** (homoscedasticity).
    
- **Residuals vs. Clutter & Filter:** The spread of residuals is approximately equal across all levels of these primary factors. This shows that the model's error is independent of the treatment levels.
    
- **Residuals vs. Operator & Day:** There are no unusual patterns or significantly different spreads across the different operators or days. This confirms that the **blocking factors** (Operators and Days) were effectively handled by the Latin Square design and do not introduce bias into the error term.
    

---

## 🔗 Part 4: Incomplete Blocks and Confounding

### 4.1 The Problem: When Blocks Are Too Small

**Scenario**: 2^2 factorial design (4 treatments: (1), a, b, ab)

- But we can only run **2 treatments per block** (e.g., 2 runs per day)
    
- We need **2 blocks** to run all 4 treatments
    

**Question**: Which 2 treatments should go in Block 1, and which 2 in Block 2?

---

### 4.2 Option 1: Confounding the AB Interaction

#### **The Design Setup**

The page proposes a specific split:

- **Block 1:** Includes the treatment combinations `(1)` (both factors low) and `ab` (both factors high).
    
- **Block 2:** Includes `a` (only A high) and `b` (only B high).
    

**Design**:

|Block|Treatments|
|---|---|
|**1**|(1), ab|
|**2**|a, b|

**Why this choice?** Let's calculate the block effect:

$$  
\text{Block Effect} = \text{Average in Block 1} - \text{Average in Block 2}  
$$

  

$$  
= \frac{(1) + ab}{2} - \frac{a + b}{2} = \frac{(1) + ab - a - b}{2}  
$$

  

Now recall the formula for the **AB interaction effect**:

$$  
AB = \frac{(1) + ab - a - b}{2n}  
$$

  

For n=1 replicate: AB = \frac{(1) + ab - a - b}{2}

**Day Effect** (the difference between Day 1 and Day 2) becomes exactly the same as the math for the **Interaction Effect (AB)**.

✅ **Conclusion**: **Block Effect = AB Interaction Effect**

> 🔶 **This is CONFOUNDING**: We cannot tell if a difference between blocks is due to:
> 
> - The block nuisance factor (e.g., different days effect), OR
>     
> - The AB interaction between factors A and B
>     

---

### 4.3 Visualizing Confounding with the ± Sign Table

|Treatment|I|A|B|**AB**|**Block**|
|---|---|---|---|---|---|
|(1)|+|−|−|**+**|**1**|
|a|+|+|−|**−**|**2**|
|b|+|−|+|**−**|**2**|
|ab|+|+|+|**+**|**1**|

✅ **Key Observation**:

- All treatments with **AB = +** are in **Block 1**
    
- All treatments with **AB = −** are in **Block 2**
    
- The AB column is identical to the Block assignment → **AB is confounded with blocks**
    
- It is fine AB is confounded with blocks if we feel that interactions are not an issue, and don’t want to estimate them.
    

### Question

![image-20260404155458283](file:///D:/Obsidian/repo/Ualberta/STAT-368/assets/image-20260404155458283.png?lastModify=1776320678)

#### The First Question: "What effect is confounded with blocks?"

**The Answer:** In this specific table, **Factor A is confounded with blocks**. Because every time you run the "Low" level of A, you are in Block 1, and every time you run the "High" level of A, you are in Block 2. You won't be able to tell if a change in the result is because of Factor A or because of the difference between the two blocks.

---

### 4.4 When Is Confounding Acceptable?

✅ **Confounding is OK if**:

- You believe the confounded effect is negligible (e.g., high-order interactions like ABC)
    
- You are primarily interested in main effects and low-order interactions
    
- You have limited resources and must block
    

❌ **Confounding is BAD if**:

- The confounded effect might be important
    
- You need to estimate that specific effect
    

> 💡 **Rule of Thumb**: Confound the highest-order interactions you're willing to assume are zero.

---

### 4.5 Algebraic Method: Defining Contrasts (For Any 2^k Design)

**Goal**: Systematically assign treatments to blocks while controlling which effects are confounded.

#### Step-by-Step Algorithm:

**For 2 blocks (2^1 blocks)**:

1. Choose the effect to confound (e.g., ABC)
    
2. Write the **defining contrast**:
    
    $$  
    L = \alpha_1 x_1 + \alpha_2 x_2 + \cdots + \alpha_k x_k  
    $$
    
      
    
    Where:
    
    - x_i = 1 if factor i is at high level, 0 if low level
        
    - \alpha_i = 1 if factor i appears in the effect to confound, 0 otherwise
        
3. Evaluate L for each treatment combination using **arithmetic mod 2**:
    
    - x \pmod{2} = remainder when x is divided by 2
        
    - Examples: 0 \pmod{2} = 0, 1 \pmod{2} = 1, 2 \pmod{2} = 0, 3 \pmod{2} = 1
        
4. Assign treatments:
    
    - L = 0 \pmod{2} → Block 1
        
    - L = 1 \pmod{2} → Block 2
        

---

#### Example: Confounding ABC in a 2^3 Design

Question: Consider a 2^3 factorial design in each of 2 blocks. Can we confound ABC with blocks?

**Step 1**: Effect to confound = ABC = A^1B^1C^1 → \alpha_1 = \alpha_2 = \alpha_3 = 1

**Step 2**: Defining contrast:

$$  
L = 1\cdot x_1 + 1\cdot x_2 + 1\cdot x_3 = x_1 + x_2 + x_3  
$$

  

**Step 3**: Evaluate L \pmod{2} for all 8 treatments:

![image-20260404160410070](file:///D:/Obsidian/repo/Ualberta/STAT-368/assets/image-20260404160410070.png?lastModify=1776320678)

|Treatment|x_1 (A)|x_2 (B)|x_3 (C)|L = x_1+x_2+x_3|L \pmod{2}|Block|
|---|---|---|---|---|---|---|
|(1)|0|0|0|0|**0**|1|
|a|1|0|0|1|**1**|2|
|b|0|1|0|1|**1**|2|
|ab|1|1|0|2|**0**|1|
|c|0|0|1|1|**1**|2|
|ac|1|0|1|2|**0**|1|
|bc|0|1|1|2|**0**|1|
|abc|1|1|1|3|**1**|2|

**Step 4**: Final block assignment:

- **Block 1**: (1), ab, ac, bc
    
- **Block 2**: a, b, c, abc
    

✅ **Verification**: Check the ± sign table for ABC column — it matches the block assignment!

|**Treatment Combinations**|**I**|**A**|**B**|**AB**|**C**|**AC**|**BC**|**ABC**|**Block**|
|---|---|---|---|---|---|---|---|---|---|
|**(1)**|+|-|-|+|-|+|+|-|**1**|
|**a**|+|+|-|-|-|-|+|+|**2**|
|**b**|+|-|+|-|-|+|-|+|**2**|
|**ab**|+|+|+|+|-|-|-|-|**1**|
|**c**|+|-|-|+|+|-|-|+|**2**|
|**ac**|+|+|-|-|+|+|-|-|**1**|
|**bc**|+|-|+|-|+|-|+|-|**1**|
|**abc**|+|+|+|+|+|+|+|+|**2**|

---

### 4.6 For More Than 2 Blocks (2^p Blocks)

**General Rule**: To create 2^p blocks, choose p effects to confound.

**Example**: 2^4 factorial design in 4 = 2^2 blocks → choose 2 effects to confound.

**Procedure**:

1. Choose two effects to confound (e.g., ABC and ACD)
    
2. Create two defining contrasts:
    
    $$  
    L_1 = x_1 + x_2 + x_3 \quad \text{(for ABC)}  
    $$
    
      
    
    $$  
    L_2 = x_1 + x_3 + x_4 \quad \text{(for ACD)}  
    $$
    
      
    
3. Evaluate (L_1, L_2) \pmod{2} for each treatment
    
4. The 4 possible combinations define the 4 blocks:
    
    - (0,0) → Block I
        
    - (1,0) → Block II
        
    - (0,1) → Block III
        
    - (1,1) → Block IV
        

**Important**: If you confound effects E_1 and E_2, you also automatically confound their product E_1 \times E_2 (using "mod 2 multiplication" where A^2 = 1).

Example: Confounding ABC and ACD also confounds:

$$  
ABC \times ACD = A^2BC^2D = BD  
$$

  

---

## 🔬 Part 5: Example 3 - Filtration Rate Experiment (2^4 in Two Blocks)

### 5.1 Background

Recall the filtration rate experiment from Chapter 6:

- **4 factors**: A=Temperature, B=Pressure, C=Concentration, D=Stirring Rate
    
- **Response**: Filtration rate (gal/h)
    
- **Design**: 2^4 = 16 treatment combinations
    

**New constraint**: Can only run 8 treatments per block (e.g., two batches of formaldehyde)

**Choice**: Confound the 4-way interaction ABCD with blocks (reasonable assumption: 4-way interactions are usually negligible)

---

### 5.2 Block Assignment Using Defining Contrast

**Effect to confound**: ABCD = A^1B^1C^1D^1 → all \alpha_i = 1

**Defining contrast**:

$$  
L = x_1 + x_2 + x_3 + x_4  
$$

  

**Block assignment** (L \pmod{2}):

- L = 0: (1), ab, ac, ad, bc, bd, cd, abcd → **Block 1**
    
- L = 1: a, b, c, d, abc, abd, acd, bcd → **Block 2**
    

---

### 5.3 Data Table (Modified to Simulate Block Effect)

_Note: 20 was subtracted from all Block 1 observations to simulate inferior first batch_

|Run|Label|A|B|C|D|ABCD|Filtration Rate|Block|
|---|---|---|---|---|---|---|---|---|
|1|(1)|−|−|−|−|+|**25**|1|
|2|a|+|−|−|−|−|71|2|
|3|b|−|+|−|−|−|48|2|
|4|ab|+|+|−|−|+|**45**|1|
|5|c|−|−|+|−|−|68|2|
|6|ac|+|−|+|−|+|**40**|1|
|7|bc|−|+|+|−|+|**60**|1|
|8|abc|+|+|+|−|−|65|2|
|9|d|−|−|−|+|−|43|2|
|10|ad|+|−|−|+|+|**80**|1|
|11|bd|−|+|−|+|+|**25**|1|
|12|abd|+|+|−|+|−|104|2|
|13|cd|−|−|+|+|+|**55**|1|
|14|acd|+|−|+|+|−|86|2|
|15|bcd|−|+|+|+|−|70|2|
|16|abcd|+|+|+|+|+|**76**|1|

**R Code to Enter Data**:

y <- c(25, 71, 48, 45, 68, 40, 60, 65,  # Block 1 & 2 mixed  
       43, 80, 25, 104, 55, 86, 70, 76)  
  
# Create coded factor variables (-1 = low, +1 = high)  
A <- rep(c(-1, 1), times = 8)  
B <- rep(c(-1, 1), each = 2, times = 4)    
C <- rep(c(-1, 1), each = 4, times = 2)  
D <- rep(c(-1, 1), each = 8)  
  
# Calculate ABCD interaction column  
ABCD <- A * B * C * D  # Product of coded values  
  
# Create block factor based on ABCD sign  
Blocks <- as.factor(ABCD)  # +1 → Block 1, -1 → Block 2  
  
# Convert to factors for modeling  
A <- as.factor(A);   
B <- as.factor(B);  
C <- as.factor(C);   
D <- as.factor(D);

### R Codes to Print Data:

data <- data.frame(A, B, C, D, ABCD, y)  
data

![image-20260404193842657](file:///D:/Obsidian/repo/Ualberta/STAT-368/assets/image-20260404193842657.png?lastModify=1776320678)

### R Codes for Half-Normal Plot:

# Fit the full model with all 4 factors and all possible interactions  
model = lm(y ~ (A+B+C+D)^4)  
  
# Display the ANOVA table to see initial results  
anova(model)  
  
# Extract the absolute value of the estimated effects (excluding the intercept)  
model.effects = abs(model$effects[-1])  
  
# Create the Half-Normal Plot  
# Note: "type='n'" tells R to set up the plot without drawing the points yet  
qq <- qqnorm(model.effects, type="n")  
  
# Label the points with their factor names so we can identify them  
text(qq$x, qq$y, labels = names(model.effects))

![image-20260404194348980](file:///D:/Obsidian/repo/Ualberta/STAT-368/assets/image-20260404194348980.png?lastModify=1776320678)

**The Noise (Bottom Left):** The points clustered in a straight line at the bottom (like `A1:B1`, `B1:D1`, `C1:A1:B1`) represent **insignificant effects**. These are minor variations that don't truly impact the filtration rate.

**The Significant Factors (Outliers):** Points that jump far away from the diagonal line are the "winners" that significantly change the filtration rate:

- **A1 (Temperature):** The highest point, meaning Temperature has the single biggest impact.
    
- **D1 (Stirring Rate):** Also very high and significant.
    
- **C1 (Concentration):** Significant, but less impactful than A or D.
    
- **AC (A1:C1) & AD (A1:D1):** These are **interactions**. They show that the effect of Temperature changes depending on the Concentration and Stirring Rate.
    

**B1 (Pressure):** Notice `B1` is buried in the noise at the bottom. This tells the engineer that **Pressure does not significantly affect the filtration rate**.

---

### 5.4 Analysis Strategy

**Problem**: ABCD is confounded with blocks → we cannot estimate ABCD separately from block effect.

**Solution**:

1. Include Blocks as a factor in the model
    
2. Fit model with all important effects EXCEPT ABCD
    
3. Use remaining degrees of freedom to estimate error
    

**R Code**:

# Fit model: main effects + important 2-way interactions + Blocks  
model <- lm(y ~ A + C + D + A*C + A*D + Blocks)  
anova(model)

**ANOVA Table**:

|Source|df|Sum Sq|Mean Sq|F value|Pr(>F)|Significance|
|---|---|---|---|---|---|---|
|A|1|1870.56|1870.56|89.76|5.60e-06|***|
|C|1|390.06|390.06|18.72|0.0019|**|
|D|1|855.56|855.56|41.05|0.00012|***|
|**Blocks**|**1**|**1387.56**|**1387.56**|**66.58**|**1.89e-05**|***|
|A:C|1|1314.06|1314.06|63.05|2.35e-05|***|
|A:D|1|1105.56|1105.56|53.05|4.65e-05|***|
|**Residuals**|**9**|**187.56**|**20.84**|—|—|—|

✅ **Interpretation**:

- **Blocks effect is highly significant** (p < 0.001) → the two batches of formaldehyde really did differ!
    
- Main effects A, C, D and interactions A:C, A:D are all significant
    
- We successfully separated treatment effects from block effects by confounding only the negligible ABCD interaction
    

**The Threshold:** In statistics, we typically look for a p-value **less than 0.05**.

- If P < 0.05: The factor is **Significant** (it truly affects the filtration rate).
    
- If P > 0.05: The factor is **Insignificant** (any change is likely just random luck).
    

**Individual Factor Results:**

- **A (Temperature):** P = 5.600 \times 10^{-6} (Extremely small). This is the most significant factor.
    
- **D (Stirring Rate):** P = 0.0001242. Also highly significant.
    
- **C (Concentration):** P = 0.0019155. Significant, but has a smaller impact than A or D.
    

**Interaction Results:**

- **A:C and A:D:** Both have very small p-values (< 0.0001). This tells us that Temperature's effect **depends** on the Concentration and the Stirring Rate. You cannot look at Temperature in a vacuum.
    

**The Block Result:**

- **Blocks:** P = 1.889 \times 10^{-5}. This low p-value proves that the **"Batch" of chemicals** actually mattered. Using a blocking design was the right move; otherwise, this "batch noise" would have blurred the results of your main factors.
    
- The background variable you were worried about (e.g., different **Batches** of chemicals or different **Operators**) actually caused a real difference in the results.
    

---

### 5.5 Half-Normal Plot for Effect Screening

**R Code**:

# Fit saturated model to extract all effects  
model_full <- lm(y ~ (A+B+C+D)^4)  
  
# Extract effects (exclude intercept) and take absolute values  
effects <- abs(model_full$effects[-1])  
  
# Create half-normal plot  
qq <- qqnorm(effects, type = "n")  
text(qq$x, qq$y, labels = names(effects))  
abline(0, 1, col = "red")

✅ **Result**: Effects A, C, D, A:C, A:D, and Blocks stand out from the line → these are the important terms.

---

## 🔬 Part 6: Example 4 - 2^4 Design in Four Blocks

![image-20260404200617302](file:///D:/Obsidian/repo/Ualberta/STAT-368/assets/image-20260404200617302.png?lastModify=1776320678)

### 6.1 New Constraint

Now we can only run **4 treatments per block** → need 16/4 = 4 blocks.

**Choice**: Confound two effects: ABC and ACD

- This also automatically confounds their product: ABC \times ACD = BD
    

### 6.2 Block Assignment Using Two Defining Contrasts

**Defining contrasts**:

$$  
L_1 = x_1 + x_2 + x_3 \quad \text{(for ABC)}  
$$

  

$$  
L_2 = x_1 + x_3 + x_4 \quad \text{(for ACD)}  
$$

  

**Block assignment** based on (L_1, L_2) \pmod{2}:

|Treatment|x_1|x_2|x_3|x_4|L_1|L_2|(L_1,L_2)|Block|
|---|---|---|---|---|---|---|---|---|
|(1)|0|0|0|0|0|0|(0,0)|**I**|
|a|1|0|0|0|1|1|(1,1)|**IV**|
|b|0|1|0|0|1|0|(1,0)|**II**|
|ab|1|1|0|0|0|1|(0,1)|**III**|
|c|0|0|1|0|1|1|(1,1)|**IV**|
|ac|1|0|1|0|0|0|(0,0)|**I**|
|bc|0|1|1|0|0|1|(0,1)|**III**|
|abc|1|1|1|0|1|0|(1,0)|**II**|
|d|0|0|0|1|0|1|(0,1)|**III**|
|ad|1|0|0|1|1|0|(1,0)|**II**|
|bd|0|1|0|1|1|1|(1,1)|**IV**|
|abd|1|1|0|1|0|0|(0,0)|**I**|
|cd|0|0|1|1|1|0|(1,0)|**II**|
|acd|1|0|1|1|0|1|(0,1)|**III**|
|bcd|0|1|1|1|0|0|(0,0)|**I**|
|abcd|1|1|1|1|1|1|(1,1)|**IV**|

**Final Block Structure**:

- **Block I**: (1), ac, abd, bcd
    
- **Block II**: b, abc, ad, cd
    
- **Block III**: ab, bc, d, acd
    
- **Block IV**: a, c, bd, abcd
    

---

### 6.3 R Code to Create Blocks Programmatically

# After creating A, B, C, D as coded variables (-1/+1)  
ABC <- A * B * C   # Calculate ABC interaction column  
ACD <- A * C * D   # Calculate ACD interaction column  
BD <- B * D        # This is automatically confounded too  
  
# Assign blocks based on signs of ABC and ACD  
blocks <- vector(length = 16)  
for(i in 1:16) {  
  if(ABC[i] == -1 & ACD[i] == -1) blocks[i] <- 1  # Block I  
  if(ABC[i] ==  1 & ACD[i] == -1) blocks[i] <- 2  # Block II    
  if(ABC[i] == -1 & ACD[i] ==  1) blocks[i] <- 3  # Block III  
  if(ABC[i] ==  1 & ACD[i] ==  1) blocks[i] <- 4  # Block IV  
}  
blocks <- as.factor(blocks)  
  
data = data.frame(A, B, C, D, blocks, ABC, ACD,BD, y)  
data

![image-20260404201315597](file:///D:/Obsidian/repo/Ualberta/STAT-368/assets/image-20260404201315597.png?lastModify=1776320678)

![image-20260404201333410](file:///D:/Obsidian/repo/Ualberta/STAT-368/assets/image-20260404201333410.png?lastModify=1776320678)

---

### R Codes for Half-Normal Plot:

#### Full model

- Use `(A+B+C+D)^4` for a **complete, unblocked** 2^4 analysis.
    

# 1. Fit the full model with all factors (A, B, C, D) and all interactions  
model = lm(y ~ (A+B+C+D)^4)  
  
# 2. View the initial ANOVA table  
anova(model)  
  
# 3. Calculate the absolute value of the estimated effects   
# (excluding the intercept at index 1)  
model.effects = abs(model$effects[-1])  
  
# 4. Set up the Normal Q-Q plot frame without drawing points (type="n")  
qq <- qqnorm(model.effects, type="n")  
  
# 5. Add the labels for each effect to the plot to identify significant factors  
text(qq$x, qq$y, labels = names(model.effects))

![image-20260404202540659](file:///D:/Obsidian/repo/Ualberta/STAT-368/assets/image-20260404202540659.png?lastModify=1776320678)

This is a **Half-Normal Probability Plot**, used to distinguish "real" effects from random experimental noise.

- **The "Noise" Line:** Points that fall along the straight diagonal line represent factors and interactions that have almost no effect. Their variation is just random error.
    
- **Significant Factors (Outliers):** Points that deviate far to the right of the line are statistically significant. In this plot, factors like **A**, **C**, and interactions like **B:D** and **A:B:C** are clearly separated from the line.
    
- **The Goal:** Researchers use this visual tool to decide which factors to keep in the final model. For example, any factor still sitting on the line (like the cluster in the bottom-left) can be safely ignored or "removed" from the model to simplify the results.
    

#### **Reduced/Custom model**:

In your 2^4 blocked design (Example 4), you confounded `ABC` and `ACD` with blocks. This automatically confounds their product: ABC \times ACD = A^2BC^2D = BD Since `BD` and `ACD` are **aliased with blocks**, you cannot estimate them separately from block effects. Therefore:

- You **must drop** `B:D` and `A:C:D` from the model (as `model1` does).
    
- You **include `blocks`** to capture the batch-to-batch variation.
    
- `model1` is the **correct analysis model** for that specific blocked design.
    
- Use `model1` when you have **blocked the experiment**, deliberately **removed confounded/negligible interactions**, and want to isolate treatment effects from block variation.
    

# 1. Fit the model including blocks, main effects, and specific interactions  
model1 = lm(y ~ blocks + A + B + C + D +   
            A*B + A*C + A*D + B*C + C*D +   
            A*B*D + B*C*D + A*B*C*D)  
  
# 2. Generate the ANOVA table  
anova(model1)  
  
# 3. Calculate absolute value of effects (excluding intercept)  
model.effects = abs(model1$effects[-1])  
  
# 4. Create the Half-Normal Plot frame  
qq <- qqnorm(model.effects, type="n")  
  
# 5. Label the points to identify significant factors  
text(qq$x, qq$y, labels = names(model.effects))

![image-20260404203048560](file:///D:/Obsidian/repo/Ualberta/STAT-368/assets/image-20260404203048560.png?lastModify=1776320678)

This **Half-Normal Probability Plot** is used to visually separate "real" physical effects from random experimental error (noise).

- **The Straight Line (Noise):** The points clustered at the bottom-left (like `A:C`, `A:B:C:D`, and `B:C:D`) follow a straight diagonal line. These represent **insignificant effects**—their values are so small they are likely just random variation.
    
- **The Outliers (Significant Effects):** Points that "jump" away from the straight line are the factors that truly matter.
    
    - **`blocks`**: This is the highest point on the plot, showing that the difference between batches/blocks had the **largest impact** on the result. This confirms that blocking was essential.
        
    - **`A`, `C`, and `B`**: These main factors are also clearly off the line, meaning Temperature, Concentration, and Pressure significantly affected the filtration rate in this specific setup.
        
    - **`A:D` and `B:C`**: These interactions are slightly off the line, suggesting they may have a minor but measurable impact.
        

### 6.4 ANOVA with Four Blocks

**R Code**:

# Fit model with blocks + important effects (excluding confounded ones)  
model <- lm(y ~ blocks + A + B + C + D + B*C + A*B + A*D + C*D)  
anova(model)

**ANOVA Table** (Key Results):

|Source|df|Sum Sq|Mean Sq|F value|Pr(>F)|
|---|---|---|---|---|---|
|blocks|3|3787.7|1262.56|156.60|0.00013|
|A|1|1105.6|1105.56|137.12|0.00030|
|B|1|826.6|826.56|102.52|0.00054|
|C|1|885.1|885.06|109.78|0.00047|
|D|1|33.1|33.06|4.10|0.113|
|B:C|1|217.6|217.56|26.98|0.0065|
|A:B|1|95.1|95.06|11.79|0.0264|
|A:D|1|540.6|540.56|67.05|0.0012|
|C:D|1|60.1|60.06|7.45|0.0525|

✅ **Interpretation**:

- **Blocks effect is huge** (F = 156.6) → strong batch-to-batch variation
    
- Factors A, B, C are highly significant; D is not significant alone
    
- Important interactions: A:B, B:C, A:D
    
- **Best combination**: From interaction plots, A high, B low, C high, D high gives maximum filtration rate
    

### R Codes for Interaction Plots

# Define the model with specific main effects and interactions  
model2 = lm(y ~ blocks + A + B + C + D + B*C + A*B + A*D + C*D)  
  
# Set up a 2x2 plotting grid  
par(mfrow=c(2,2))  
  
# Generate interaction plots for specific factor pairs  
interaction.plot(A, B, y)  
interaction.plot(A, D, y)  
interaction.plot(B, C, y)  
interaction.plot(C, D, y)

![image-20260404204300009](file:///D:/Obsidian/repo/Ualberta/STAT-368/assets/image-20260404204300009.png?lastModify=1776320678)

The **Interaction Plots** are used to determine if the effect of one factor depends on the level of another factor.

- **Non-Parallel Lines:** If the lines in the plot cross or have significantly different slopes (like the **A:D** and **B:C** plots), it indicates a **strong interaction**. For example, in the A:D plot, the effect of factor A changes drastically depending on whether D is at its high (+1) or low (-1) level.
    
- **Parallel Lines:** If the lines are roughly parallel, there is little to no interaction between those two factors.
    
- **Goal:** These plots help identify the "best combination" of settings. As noted at the bottom of the page, the objective is to find the levels that maximize or minimize the response (y). In this case, the slide concludes that the optimal settings are **A, C, and D at high levels** and **B at a low level**.
    
    - The conclusion **"A, C, D high and B low"** tells the engineer: "To get the best results, run your reaction at high temperature, high concentration, and high stirring speed, but keep the pressure low."
        

---

### 6.5 Using the FrF2 Package for Automated Blocking

**R Package**: `FrF2` generates optimal blocked factorial designs.

**Example**: Create 2^4 design in blocks of size 4, confounding ABD and BCD:

#### R code to find **Design Matrix** for a 2^4 experiment split into blocks of size 4.

library(FrF2)  
  
design <- FrF2(nfactors = 4,    # 4 factors: A, B, C, D  
               nruns = 16,       # 16 total runs  
               blocks = c("ABD", "BCD"),  # Effects to confound  
               alias.block.2fis = TRUE,   # Show which 2-factor interactions are confounded  
               randomize = FALSE)         # Keep order for teaching  
  
print(design)

**Output** (first few rows):

  run.no run.no.std.rp Blocks  A  B  C  D  
1      1         1.1.1      1 -1 -1 -1 -1  
2      2         7.1.2      1 -1  1  1 -1    
3      3        12.1.3      1  1 -1  1  1  
4      4        14.1.4      1  1  1 -1  1  
...

![image-20260404205724768](file:///D:/Obsidian/repo/Ualberta/STAT-368/assets/image-20260404205724768.png?lastModify=1776320678)

✅ **Advantage**: The package automatically chooses confounding schemes that minimize loss of important effects (based on Sun et al.'s optimal blocking criteria).

The table you see on Page 44 is a **Design Matrix** for a 2^4 experiment split into blocks of size 4.

- **`Blocks` Column:** Notice the numbers 1 and 2. This shows which runs belong to which group (e.g., which runs are done on "Day 1" vs "Day 2").
    
- **The Settings (A, B, C, D):** The -1 and +1 represent the low and high levels of your factors.
    
- **The Logic:** R has chosen these specific combinations so that the main effects (A, B, C, D) are "clear" (not confounded), while less important, complex interactions are "confounded" with the blocks.
    

---

## 🔀 Part 7: Partial Confounding (Getting the Best of Both Worlds)

### 7.1 The Problem with Complete Confounding

**Complete confounding**: Same effect confounded with blocks in every replicate.

- ❌ You permanently lose information about that effect
    

**Example**: If ABC is confounded in all 3 replicates, you can NEVER estimate ABC.

---

### 7.2 Solution: Partial Confounding

**Idea**: Confound DIFFERENT effects in different replicates.

**Example from Lecture**: 2^3 design, 3 replicates, 2 blocks per replicate (4 runs/block)

![image-20260404213120415](file:///D:/Obsidian/repo/Ualberta/STAT-368/assets/image-20260404213120415.png?lastModify=1776320678)

|Replicate|Effect Confounded with Blocks|
|---|---|
|**I**|ABC|
|**II**|AB|
|**III**|BC|

**Advantage**:

- ABC is estimable from Replicates II and III (where it's not confounded)
    
- AB is estimable from Replicates I and III
    
- BC is estimable from Replicates I and II
    
- All effects can be estimated by pooling information across replicates!
    

---

### 7.3 Example 5: Machine Tool Life Experiment

![image-20260404213826879](file:///D:/Obsidian/repo/Ualberta/STAT-368/assets/image-20260404213826879.png?lastModify=1776320678)

**Factors**:

- A: Cutting speed (low/high)
    
- B: Tool geometry (type 1/type 2)
    
- C: Cutting angle (small/large)
    
- Response: Tool life (hours)
    

**Design**: 2^3 factorial, 3 replicates, 2 blocks per replicate (4 runs/block)

**Confounding scheme**:

- Rep I: Confound ABC
    
- Rep II: Confound AB
    
- Rep III: Confound BC
    

---

### 7.4 Data Table

![image-20260404214134572](file:///D:/Obsidian/repo/Ualberta/STAT-368/assets/image-20260404214134572.png?lastModify=1776320678)

|Treatment|Rep I Block 1|Rep I Block 2|Rep II Block 3|Rep II Block 4|Rep III Block 5|Rep III Block 6|
|---|---|---|---|---|---|---|
|**(1)**|22|—|31|—|25|—|
|**a**|—|32|—|43|29|—|
|**b**|—|35|—|34|—|50|
|**ab**|55|—|47|—|—|46|
|**c**|—|44|45|—|—|38|
|**ac**|40|—|—|37|—|36|
|**bc**|60|—|—|50|54|—|
|**abc**|—|39|41|—|47|—|

**Block assignments** (based on confounding scheme):

- **Rep I** (ABC confounded):
    
    - Block 1 (ABC=+): (1), ab, ac, bc
        
    - Block 2 (ABC=−): a, b, c, abc
        
- **Rep II** (AB confounded):
    
    - Block 3 (AB=+): (1), ab, abc, c
        
    - Block 4 (AB=−): a, b, ac, bc
        
- **Rep III** (BC confounded):
    
    - Block 5 (BC=+): (1), a, bc, abc
        
    - Block 6 (BC=−): ab, b, c, ac
        

|Effect|Can estimate from...|Lost in...|
|---|---|---|
|**ABC**|Rep II, Rep III|Rep I|
|**AB**|Rep I, Rep III|Rep II|
|**BC**|Rep I, Rep II|Rep III|
|**A, B, C, AC**|All 3 replicates|Nowhere!|

This is **partial confounding** - you sacrifice different effects in different replicates so you can recover information about ALL effect

---

### R Codes to Define Factors and Print Data:

Notice:

You might expect the standard Yates order: `(1), a, b, ab, c, ac, bc, abc`. **But this code does NOT use standard order.** It uses the **block order** from your lecture's partial confounding design:

**Within each replicate, the runs are entered as:** `Block 1 treatments` → `Block 2 treatments`

|Run Order in R|Treatment Label|A Level|B Level|C Level|
|---|---|---|---|---|
|1|`(1)`|`-1`|`-1`|`-1`|
|2|`ab`|`+1`|`+1`|`-1`|
|3|`ac`|`+1`|`-1`|`+1`|
|4|`bc`|`-1`|`+1`|`+1`|
|5|`a`|`+1`|`-1`|`-1`|
|6|`b`|`-1`|`+1`|`-1`|
|7|`c`|`-1`|`-1`|`+1`|
|8|`abc`|`+1`|`+1`|`+1`|

If you read down the columns for runs 1–8, you get **exactly** the patterns in your code:

- `A`: `-1, 1, 1, -1, 1, -1, -1, 1`
    
- `B`: `-1, 1, -1, 1, -1, 1, -1, 1`
    
- `C`: `-1, -1, 1, 1, -1, -1, 1, 1`
    

# Enter response data (in order by replicate and block)  
y <- c(22, 55, 40, 60,    # Rep I, Block 1  
       32, 35, 44, 39,    # Rep I, Block 2  
       31, 45, 47, 41,    # Rep II, Block 3  
       43, 34, 37, 50,    # Rep II, Block 4    
       25, 29, 54, 47,    # Rep III, Block 5  
       50, 38, 46, 36)    # Rep III, Block 6  
  
# Create coded factor variables  
A <- c(-1,1,1,-1,1,-1,-1,1,  # Rep I  
       -1,1,1,-1,1,-1,-1,1,  # Rep II  
       -1,1,1,-1,1,-1,-1,1)  # Rep III  
B <- c(-1,1,-1,1,-1,1,-1,1,  
       -1,1,-1,1,-1,1,-1,1,   
       -1,1,-1,1,-1,1,-1,1)  
C <- c(-1,-1,1,1,-1,-1,1,1,  
       -1,-1,1,1,-1,-1,1,1,  
       -1,-1,1,1,-1,-1,1,1)  
  
data = data.frame(A, B, C, Rep, Block, ABC, AB,BC, y)  
data

![image-20260404221622000](file:///D:/Obsidian/repo/Ualberta/STAT-368/assets/image-20260404221622000.png?lastModify=1776320678)

---

### 7.6 Calculating Sum of Squares for Nested Blocks

#### 1. The Core Concept: Nesting

The phrase **"Block is nested within Rep"** means that the physical environment or conditions of a block only exist inside a specific replicate.

- Because you chose to confound **different** interactions in each replicate (ABC in Rep I, AB in Rep II, etc.), the "Blocks" in each replicate represent different mathematical partitions.
    
- Therefore, you cannot treat "Block" as a single factor across the whole experiment; you must treat it as a sub-division of the Replicate.
    

#### 2. The R Formula

The code `model = lm(y ~ Rep + Block %in% Rep + ...)` uses the `%in%` operator:

- **`Rep`**: Accounts for the variation between the three large replicates.
    
- **`Block %in% Rep`**: Tells R to calculate the variation of blocks **separately** for each replicate. This prevents R from incorrectly assuming that "Block 1" in the first group is identical to "Block 1" in the second group.
    

**Formula for SS of Blocks within Replicates**:

$$  
SS_{Block(Rep)} = SS_{ABC \text{ in Rep I}} + SS_{AB \text{ in Rep II}} + SS_{BC \text{ in Rep III}}  
$$

  

$$  
SS_{\text{Blocks(Rep)}} = 4 \sum_{i=1}^{3} \sum_{j=1}^{2} (\bar{y}_{ij\cdot} - \bar{y}_{i\cdot\cdot})^2  
$$

  

Where:

- \bar{y}_{ij\cdot} = mean of block j in replicate i
    
- \bar{y}_{i\cdot\cdot} = overall mean of replicate i
    
- 4 = number of observations per block
    

**Manual Calculation Example**:

- Rep I, Block 1 mean: (22+55+40+60)/4 = 44.25
    
- Rep I overall mean: (22+55+40+60+32+35+44+39)/8 = 40.875
    
- Contribution: 4 \times (44.25 - 40.875)^2 = 4 \times 11.39 = 45.56
    

![image-20260404223504855](file:///D:/Obsidian/repo/Ualberta/STAT-368/assets/image-20260404223504855.png?lastModify=1776320678)

**R Code to Calculate**:

# Calculate block means and replicate means  
block.means <- tapply(y, Block, mean)  # Mean for each of 6 blocks  
rep.means <- rep(tapply(y, Rep, mean), each = 2)  # Rep means repeated for 2 blocks each  
  
# Calculate SS  
SS_blocks_in_rep <- 4 * sum((block.means - rep.means)^2)  
SS_blocks_in_rep  # Result: 119.25

✅ **Verification**: This equals the sum of the confounded effect SS in each replicate:

$$  
SS_{ABC \text{ in Rep I}} + SS_{AB \text{ in Rep II}} + SS_{BC \text{ in Rep III}} = 91.125 + 0 + 28.125 = 119.25  
$$

  

---

### 7.7 Final ANOVA for Nested Blocks

**Key Concept**: Blocks are **nested within Replicates** (Block 1 in Rep I is different from Block 1 in Rep II).

**R Syntax for nesting**: `Block %in% Rep` or `Rep/Block`

# Enter response data (in order by replicate and block)  
y <- c(22, 55, 40, 60,    # Rep I, Block 1  
       32, 35, 44, 39,    # Rep I, Block 2  
       31, 45, 47, 41,    # Rep II, Block 3  
       43, 34, 37, 50,    # Rep II, Block 4    
       25, 29, 54, 47,    # Rep III, Block 5  
       50, 38, 46, 36)    # Rep III, Block 6  
  
# Create coded factor variables  
A <- c(-1,1,1,-1,1,-1,-1,1,  # Rep I  
       -1,1,1,-1,1,-1,-1,1,  # Rep II  
       -1,1,1,-1,1,-1,-1,1)  # Rep III  
B <- c(-1,1,-1,1,-1,1,-1,1,  
       -1,1,-1,1,-1,1,-1,1,   
       -1,1,-1,1,-1,1,-1,1)  
C <- c(-1,-1,1,1,-1,-1,1,1,  
       -1,-1,1,1,-1,-1,1,1,  
       -1,-1,1,1,-1,-1,1,1)  
  
# Create confounding columns  
ABC <- A*B*C  
AB <- A*B    
BC <- B*C  
  
# Create factors  
Rep <- as.factor(rep(c("I","II","III"), each = 8))  
Block <- as.factor(rep(1:6, each = 4))  # 6 blocks total  
A <- as.factor(A); B <- as.factor(B); C <- as.factor(C)  
  
# Fit model with nested blocks  
model <- lm(y ~ Rep + Block %in% Rep + A + B + C + A*B + A*C + B*C + A*B*C)  
anova(model)

#### **Full Model ANOVA**:

|Source|df|Sum Sq|Mean Sq|F value|Pr(>F)|
|---|---|---|---|---|---|
|Rep|2|0.58|0.29|0.008|0.992|
|A|1|0.67|0.67|0.018|0.896|
|**B**|**1**|**770.67**|**770.67**|**20.77**|**0.00082**|
|**C**|**1**|**280.17**|**280.17**|**7.55**|**0.0190**|
|Rep:Block|3|119.25|39.75|1.07|0.401|
|A:B|1|25.00|25.00|0.67|0.429|
|**A:C**|**1**|**468.17**|**468.17**|**12.62**|**0.0045**|
|B:C|1|22.56|22.56|0.61|0.452|
|A:B:C|1|0.06|0.06|0.002|0.968|
|Residuals|11|408.21|37.11|—|—|

1. Significant Effects (Look for the stars `***`)
    

- **Factor B (p-value = 0.0008):** This is highly significant. Changes in Factor B have a very strong impact on the response y.
    
- **Factor C (p-value = 0.0189):** Also significant. Factor C matters, though less so than B.
    
- **Interaction A:C (p-value = 0.0045):** This is very significant. It means the effect of A depends heavily on the level of C. Even though **A** by itself is not significant (p = 0.89), it is critical because of how it interacts with C.
    

2. The "Block" Results
    

- **Rep (p-value = 0.992):** There is almost zero difference between the three main replicates. This is good; it means the "Day" or "Batch" didn't shift the results overall.
    
- **Rep:Block (p-value = 0.400):** This represents the **confounded interactions** (ABC, AB, and BC) that you "sacrificed." Since the p-value is high (0.40), it suggests that those interactions were likely **zero or negligible**, confirming that your choice to confound them was a safe bet.
    

3. Non-Significant Terms
    

- **Factor A (Main Effect):** Not significant on its own.
    
- **A:B and B:C Interactions:** Not significant. These factors act independently of each other.
    
- **A:B:C Interaction (p-value = 0.968):** This is the "3-way" interaction. It is clearly non-significant, which is standard for most 2^k designs.
    

#### **Simplified Model** (keep only significant terms):

model_simple <- lm(y ~ Rep + Block %in% Rep + A + B + C + A*C)  
anova(model_simple)

**Simplified ANOVA**:

|Source|df|Sum Sq|Mean Sq|F value|Pr(>F)|
|---|---|---|---|---|---|
|Rep|2|0.58|0.29|0.009|0.991|
|A|1|0.67|0.67|0.021|0.888|
|**B**|**1**|**770.67**|**770.67**|**23.67**|**0.00025**|
|**C**|**1**|**280.17**|**280.17**|**8.60**|**0.0109**|
|Rep:Block|3|119.25|39.75|1.22|0.339|
|**A:C**|**1**|**468.17**|**468.17**|**14.38**|**0.00198**|
|Residuals|14|455.83|32.56|—|—|

1. The Significant "Winners" (Look for `***` and `*`)
    

- **Factor B (p = 0.00025):** Highly significant. This is your most important factor.
    
- **Interaction A:C (p = 0.00198):** Very significant. This means the effect of A changes depending on the level of C. Even though **A** itself has a high p-value (0.88), you **must** keep A in your model because its interaction with C is so strong.
    
- **Factor C (p = 0.01089):** Significant. It has a direct effect on the result.
    

2. The Nuisance Factors (Rep and Block)
    

- **Rep (p = 0.991):** Not significant. There was no difference in the average results between Replicate I, II, and III. This is great; it means your experiment was consistent over time.
    
- **Rep:Block (p = 0.338):** Not significant. This row contains the **confounded interactions** (the ones you "sacrificed" like ABC, AB, and BC). Since this is not significant, it confirms that those complex interactions were indeed tiny/zero and you didn't lose any important information by confounding them.
    

### Assumption

![image-20260404224501265](file:///D:/Obsidian/repo/Ualberta/STAT-368/assets/image-20260404224501265.png?lastModify=1776320678)

---

## 🧮 Part 8: Essential Formulas Cheat Sheet

### 8.1 Blocking and Confounding Formulas

|Concept|Formula|Explanation|
|---|---|---|
|**Block Effect**|\frac{\text{Sum in Block 1}}{n_1} - \frac{\text{Sum in Block 2}}{n_2}|Difference in average response between blocks|
|**Defining Contrast**|L = \sum \alpha_i x_i|Linear combination to assign treatments to blocks|
|**Mod 2 Arithmetic**|x \pmod{2} = \text{remainder of } x/2|Used to evaluate defining contrasts|
|**Confounded Effect SS**|SS = \frac{(\text{Contrast})^2}{2^k n}|Same as regular effect SS, but attributed to blocks|
|**Nested SS**|SS_{B(A)} = \sum n_{ij}(\bar{y}_{ij} - \bar{y}_{i})^2|Sum of squares for B within levels of A|

### 8.2 When to Use Which Design

|Design|When to Use|Block Size|Effects Confounded|
|---|---|---|---|
|**RCBD**|One nuisance factor|≥ number of treatments|None (if complete)|
|**Latin Square**|Two nuisance factors|= number of treatments|None (if complete)|
|**Incomplete Blocks (2 blocks)**|Block size = 2^{k-1}|2^{k-1}|One effect (usually highest-order interaction)|
|**Incomplete Blocks (2^p blocks)**|Block size = 2^{k-p}|2^{k-p}|p effects + their generalized interactions|
|**Partial Confounding**|Multiple replicates, want to estimate all effects|Any|Different effects in different replicates|

---

## 🛠️ Part 9: Practical Implementation Guide

### 9.1 Step-by-Step Workflow for Blocked 2^k Designs

### 9.2 R Code Template for Blocked Analysis

# ===== STEP 1: ENTER DATA =====  
y <- c(...)  # Response values  
# Create factor variables for treatments and blocks  
A <- factor(...); B <- factor(...); Blocks <- factor(...)  
  
# ===== STEP 2: FIT MODEL =====  
# For RCBD:  
model <- lm(y ~ A + B + A:B + Blocks)  
  
# For incomplete blocks with confounding:  
# Include Blocks as a factor, exclude confounded effects  
model <- lm(y ~ A + C + A:C + Blocks)  # Example: ABC confounded  
  
# ===== STEP 3: ANOVA =====  
anova(model)  
  
# ===== STEP 4: CHECK ASSUMPTIONS =====  
par(mfrow = c(2,2))  
qqnorm(model$residuals); qqline(model$residuals)  
plot(model$fitted.values, model$residuals)  
abline(h = 0, col = "red")  
  
# ===== STEP 5: INTERPRET =====  
# Look at significant effects (p < 0.05)  
# Use interaction plots to understand interactions  
interaction.plot(A, B, y, type = "b")

### 9.3 Common Mistakes to Avoid

❌ **Not randomizing within blocks** → confounding with time/order effects ❌ **Confounding important effects** → losing ability to estimate key interactions ❌ **Ignoring block effects in analysis** → inflated error variance, reduced power ❌ **Using wrong nesting syntax in R** → incorrect SS calculations ❌ **Not checking residuals** → invalid conclusions if assumptions violated

### 9.4 How to Choose Effects to Confound

**Priority Order** (confound from bottom up):

1. ✅ **4-way+ interactions** (ABCD, ABCDE, ...) — almost always negligible
    
2. ✅ **3-way interactions** (ABC, ABD, ...) — often negligible in screening
    
3. ⚠️ **2-way interactions** — only confound if you're sure they're unimportant
    
4. ❌ **Main effects** — NEVER confound main effects!
    

**Rule**: When in doubt, confound the highest-order interaction possible.

---

## 🎓 Summary: What You Should Remember

### Key Concepts

1. **Blocking** controls nuisance factors by grouping similar experimental units
    
2. **Confounding** occurs when a treatment effect cannot be separated from a block effect
    
3. **Defining contrasts** provide a systematic way to assign treatments to blocks
    
4. **Partial confounding** allows estimation of all effects by varying confounding across replicates
    
5. **Nested factors** (e.g., Blocks within Replicates) require special model syntax in R
    

### Practical Takeaways

✅ Always randomize treatment order WITHIN each block ✅ Confound only effects you believe are negligible (start with highest-order interactions) ✅ Include block factors in your ANOVA model to properly account for blocking ✅ Use half-normal plots to identify significant effects when you have many candidates ✅ Check residual plots to validate model assumptions

### When to Use This Chapter

✅ You have nuisance factors you can't eliminate but can group (operators, days, batches) ✅ You're running a factorial experiment with limited resources per block ✅ You need to screen many factors efficiently while controlling for external variation ❌ You have no nuisance factors → use completely randomized design ❌ You can run all treatments under identical conditions → no blocking needed

---

> 💡 **Final Tip for Beginners**: Start with simple RCBD examples. Master the idea that "blocks remove variability" before tackling confounding. The algebra of defining contrasts becomes intuitive once you see the pattern in the ± sign tables!

_Need clarification on any calculation, R code, or concept? Want me to walk through a specific example again? Just ask!_ 😊