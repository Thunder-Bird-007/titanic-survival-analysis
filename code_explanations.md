# Titanic Notebook — Line by Line Code Explanations
### Every unique construct explained word by word
---

## Importing Libraries

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from scipy import stats
```

**Line 1 — `import pandas as pd`**

`import` is a Python keyword that tells Python: "go find this library and load it
into memory so I can use it." `pandas` is the name of the library — it is a
third-party package (not built into Python) that gives you tools for working with
tabular data, like an Excel spreadsheet inside Python. `as pd` means: give it the
nickname `pd` so that every time you want to use it, you type `pd.something`
instead of `pandas.something`. This is purely for convenience — `pd` is the
universal convention that every data analyst in the world uses.

**Line 2 — `import numpy as np`**

`numpy` stands for Numerical Python. It is the foundational math library — it
gives you fast arrays and mathematical operations. Many other libraries (pandas,
matplotlib, seaborn) are built on top of numpy internally. `as np` gives it the
short nickname `np`, again a universal convention.

**Line 3 — `import matplotlib.pyplot as plt`**

`matplotlib` is the main plotting library in Python. Inside matplotlib there is a
sub-module called `pyplot` which contains all the chart-drawing functions.
`matplotlib.pyplot` is the full path — think of it like a folder inside a folder.
`as plt` gives it the nickname `plt`. So whenever you type `plt.something()`, you
are calling a function from inside matplotlib's pyplot module.

**Line 4 — `import seaborn as sns`**

`seaborn` is a higher-level plotting library built on top of matplotlib. It
produces prettier statistical charts with less code. `as sns` is the nickname —
the `sns` abbreviation comes from Samuel Norman Seaborn, a character from The West
Wing TV show (the library's creator was a fan). It has no technical meaning, but
`sns` is used universally.

**Line 5 — `from scipy import stats`**

`scipy` (Scientific Python) is a library of scientific and mathematical tools.
Unlike the previous lines which import the whole library, `from scipy import stats`
says: "from inside scipy, bring in only the `stats` sub-module." The `stats`
sub-module specifically contains statistical functions like chi-square tests,
t-tests, and probability distributions. After this line you can write
`stats.chi2_contingency()` directly instead of `scipy.stats.chi2_contingency()`.

---

## Loading Data and Checking Missing Values

```python
df = pd.read_csv('../data/raw/train.csv')
print(df.isnull().sum())
```

**Line 1 — `df = pd.read_csv('../data/raw/train.csv')`**

Breaking this down word by word:

`pd` is the pandas nickname from the import above. `read_csv` is a pandas function
that reads a CSV file from your computer and converts it into a DataFrame. A
DataFrame is pandas' core data structure — think of it as a Python-powered Excel
table where every row is a passenger and every column is a feature.

`('../data/raw/train.csv')` is the file path in quotes. The `..` at the start
means "go one folder up from where this notebook is." Since the notebook lives
inside the `notebooks/` folder, `..` takes you up to the project root, and then
`/data/raw/train.csv` is the path from there. This relative path is better than
an absolute path (like `C:/Users/Abrar/...`) because it works on any computer.

`df =` assigns the resulting DataFrame to a variable called `df`. The name `df` is
a universal convention for "DataFrame" — you will see it in almost every data
analysis notebook in the world.

**Line 2 — `print(df.isnull().sum())`**

This is three operations chained together, read from inside out.

`df.isnull()` — called on the entire DataFrame. For every single cell in the
table, it returns `True` if the value is missing (NaN = Not a Number, Python's
representation of a missing value) and `False` if a value exists. The result is a
DataFrame of the same shape filled with True/False values.

`.sum()` — called on the True/False DataFrame. In Python, `True` is treated as 1
and `False` as 0. So `.sum()` adds up the True values column by column, giving you
a count of missing values per column.

`print(...)` — displays the result in the notebook output. Without `print()`, the
last expression in a cell is shown automatically, but it is good practice to be
explicit.

---

## Q1: Overall Survival Rate

```python
survival_rate = df['Survived'].mean()
print(f"Survival rate: {survival_rate:.2%}")
```

**Line 1 — `df['Survived']`**

Square bracket notation on a DataFrame selects a single column by name. `'Survived'`
is the column name as a string. The result is a pandas Series — a one-dimensional
list of values with an index. Think of it as extracting one column from your Excel
table.

**`.mean()`**

A pandas method that computes the arithmetic mean of the Series. Because the
Survived column only contains 0s and 1s, the mean equals the proportion of 1s —
which is exactly the survival rate. You do not need to count survivors and divide
manually; the mean does it in one step.

`survival_rate =` stores the result (a single decimal number) in a variable.

**Line 2 — `print(f"Survival rate: {survival_rate:.2%}")`**

`f"..."` is an f-string (formatted string literal). The `f` before the opening
quote tells Python: "this string may contain expressions inside curly braces `{}`
— evaluate them and insert the result."

`{survival_rate:.2%}` is the embedded expression. `survival_rate` is the variable.
The `:` separates the variable name from the format specification. `.2%` is the
format code — `%` means multiply by 100 and append a percent sign, `.2` means show
2 decimal places. So if `survival_rate = 0.3838`, this prints `38.38%`.

---

## Q2: Survival Rate by Sex

```python
survival_by_sex = df.groupby('Sex')['Survived'].mean()
print(survival_by_sex)
```

**`df.groupby('Sex')`**

`groupby` is one of the most important pandas operations. It implements the
split-apply-combine pattern from statistics. `'Sex'` is the column to group by.
This instruction splits the entire DataFrame into sub-groups — one group containing
all female rows, one containing all male rows. Nothing is computed yet; you have
just defined the grouping.

**`['Survived']`**

After groupby, you select which column to operate on within each group. This says:
"from each group (female rows, male rows), take only the Survived column."

**`.mean()`**

Compute the mean of Survived within each group. Since Survived is 0/1, this gives
the survival rate per group. The result is a Series with Sex values as the index
and survival rates as the values.

`print(survival_by_sex)` — displays that Series.

---

## Q2: Bar Chart

```python
plt.figure(figsize=(6,4))
survival_by_sex.plot(kind='bar', color=['salmon', 'steelblue'], edgecolor='black')
plt.title('Survival Rate by Sex')
plt.xlabel('Sex')
plt.ylabel('Survival Rate')
plt.xticks(rotation=0)
plt.ylim(0,1)
plt.tight_layout()
plt.savefig('../visuals/survival_by_sex.png', dpi=150, bbox_inches='tight')
plt.show()
```

**`plt.figure(figsize=(6,4))`**

`plt.figure()` creates a new blank canvas for drawing. Without this, matplotlib
might draw on a previous chart. `figsize=(6,4)` sets the canvas size in inches —
6 inches wide, 4 inches tall. `figsize` takes a tuple (a pair of values in
parentheses separated by a comma).

**`survival_by_sex.plot(kind='bar', color=['salmon', 'steelblue'], edgecolor='black')`**

Pandas Series objects have a built-in `.plot()` method that calls matplotlib
internally. `kind='bar'` tells it to make a bar chart (other options include
`'line'`, `'pie'`, `'hist'`). `color=['salmon', 'steelblue']` is a list of two
color names — the first bar (female) gets salmon, the second (male) gets
steelblue. Color names in matplotlib can be common English names or hex codes like
`'#FF5733'`. `edgecolor='black'` draws a black border around each bar, which
improves visual clarity.

**`plt.title(...)`, `plt.xlabel(...)`, `plt.ylabel(...)`**

These three add text labels to the chart. `title` goes at the top. `xlabel` labels
the horizontal axis. `ylabel` labels the vertical axis. Each takes a string.

**`plt.xticks(rotation=0)`**

`xticks` controls the tick labels on the x-axis (the category labels below each
bar). `rotation=0` keeps them horizontal. By default, pandas sometimes rotates
them to 90 degrees which makes them hard to read. Setting it to 0 fixes that.

**`plt.ylim(0,1)`**

`ylim` sets the range of the y-axis. `(0,1)` forces the y-axis to go from 0 to 1
regardless of the data. This is important for survival rate charts — if you let
matplotlib auto-scale, it might start the axis at 0.1 which would make small
differences look massive (a misleading chart). Fixing it at 0 to 1 gives an honest
picture.

**`plt.tight_layout()`**

Automatically adjusts the spacing between the chart elements (title, labels, axes)
so that nothing gets cut off. Always call this before saving. Without it, axis
labels are sometimes clipped.

**`plt.savefig('../visuals/survival_by_sex.png', dpi=150, bbox_inches='tight')`**

Saves the chart as a PNG image file. The path `'../visuals/survival_by_sex.png'`
uses the same `..` relative path logic as before — go up from `notebooks/` to the
project root, then into `visuals/`. `dpi=150` sets the image resolution (dots per
inch) — 150 is crisp enough for portfolios without being a huge file. `bbox_inches='tight'`
prevents the edges of the saved image from clipping any labels. **Critically, you
must call `savefig` before `plt.show()`** — calling `show()` first clears the
figure from memory, so you would save a blank image.

**`plt.show()`**

Renders and displays the chart in the notebook output. After this call, the figure
is cleared from memory.

---

## Q2: Chi-Square Test

```python
contingency = pd.crosstab(df['Sex'], df['Survived'])
print(contingency)

chi2, p, dof, expected = stats.chi2_contingency(contingency)
print(f"chi2 stat : {chi2:.2f}")
print(f"p-value : {p:.4f}")
print(f"degrees of freedom : {dof}")
```

**`pd.crosstab(df['Sex'], df['Survived'])`**

`crosstab` is short for cross-tabulation. It takes two Series and builds an
observed frequency table — the same contingency table you would draw by hand when
doing a chi-square test on paper. The first argument becomes the rows, the second
becomes the columns. The result is a DataFrame where:

- Rows = unique values of Sex (female, male)
- Columns = unique values of Survived (0, 1)
- Each cell = count of passengers matching that combination

`contingency =` stores this table.

**`chi2, p, dof, expected = stats.chi2_contingency(contingency)`**

`stats.chi2_contingency()` takes the observed contingency table and performs the
full chi-square test of independence. Internally it computes expected frequencies
(assuming independence), calculates the chi-square statistic, and looks up the
p-value. It returns four values simultaneously.

`chi2, p, dof, expected =` is called tuple unpacking. The function returns four
values in a fixed order, and this single line assigns all four to four separate
variable names at once. `chi2` gets the chi-square statistic, `p` gets the
p-value, `dof` gets the degrees of freedom, and `expected` gets the matrix of
expected frequencies (you printed it for reference but do not use it further).

**`print(f"chi2 stat : {chi2:.2f}")`**

`:.2f` is a new format code. `f` means "fixed-point number" (a regular decimal,
not scientific notation). `.2` means 2 decimal places. So `260.7234` becomes
`260.72`. Compare with `:.2%` which additionally multiplies by 100 and adds a
percent sign — `:.2f` just rounds to 2 decimal places.

**`print(f"p-value : {p:.4f}")`**

`:.4f` — same as above but 4 decimal places. Used here because p-values are often
very small and you want to see enough decimal places (e.g. `0.0000` vs `0.00`).

---

## Q2: Interpret Result

```python
if (p < 0.05):
    print("Sex and survival are significantly associated.")
else:
    print("No significant association between sex and survival.")
```

**`if (p < 0.05):`**

A standard Python if-statement. `p < 0.05` is a comparison expression — it
evaluates to `True` or `False`. The parentheses around it are optional (they do
not change behavior here, just add clarity). The colon `:` at the end is required
Python syntax — it signals the start of the indented block to execute if the
condition is True.

**Indentation**

The `print(...)` lines are indented by 4 spaces. Python uses indentation to define
code blocks — everything indented under the `if` runs only when the condition is
True. The `else:` block runs when the condition is False.

---

## Q3: Survival Rate by Pclass

```python
survival_by_pclass = df.groupby('Pclass')['Survived'].mean()
print(survival_by_pclass)
```

Same pattern as Cell 9. The only difference is `'Pclass'` instead of `'Sex'`.
Now `groupby` splits into 3 groups (class 1, 2, 3) instead of 2. The result is a
Series with 3 values instead of 2.

---

## Q3: Bar Chart

```python
plt.figure(figsize=(6,4))
survival_by_pclass.plot(kind='bar', color=['gold', 'silver', 'peru'], edgecolor='black')
plt.title('Survival Rate by Passenger Class')
plt.xlabel('Passenger Class')
plt.ylabel('Survival Rate')
plt.xticks(rotation=0)
plt.ylim(0,1)
plt.tight_layout()
plt.savefig('../visuals/survival_by_pclass.png', dpi=150, bbox_inches='tight')
plt.show()
```

Same pattern as Cell 10. The only new thing is `color=['gold', 'silver', 'peru']`
— three colors for three bars. `'peru'` is a built-in matplotlib color name for a
brownish tone, chosen loosely to represent 3rd class. All other lines are identical
in structure to Cell 10 — refer to that explanation.

---

## Q3: Chi-Square Test

```python
contingency2 = pd.crosstab(df['Pclass'], df['Survived'])
print(contingency2)

chi2, p, dof, expected = stats.chi2_contingency(contingency2)
print(f"chi2 stat : {chi2:.2f}")
print(f"p-value : {p:.4f}")
print(f"degrees of freedom : {dof}")
```

Same pattern as Cell 11. The difference is `df['Pclass']` instead of `df['Sex']`,
so the contingency table now has 3 rows (classes 1, 2, 3) instead of 2. This
means `dof` will be 2 instead of 1, because degrees of freedom = (rows−1) ×
(cols−1) = (3−1) × (2−1) = 2. Everything else is identical.

---

## Q4: Split Age Data by Survival Status

```python
survived_age = df[df['Survived'] == 1]['Age'].dropna()
not_survived_age = df[df['Survived'] == 0]['Age'].dropna()
```

**`df[df['Survived'] == 1]`**

This is called boolean indexing — one of the most important pandas patterns. Let's
unpack it from the inside out.

`df['Survived']` extracts the Survived column as a Series of 0s and 1s.

`df['Survived'] == 1` compares every value in that Series to 1. The result is a
new Series of True/False values — True where Survived is 1, False where it is 0.
This is called a boolean mask.

`df[boolean_mask]` — when you put a boolean mask inside the outer `df[...]`, pandas
keeps only the rows where the mask is True and discards the rest. So
`df[df['Survived'] == 1]` gives you a new DataFrame containing only the rows of
passengers who survived.

**`['Age']`**

From that filtered DataFrame, extract just the Age column as a Series.

**`.dropna()`**

Removes any NaN (missing) values from the Series. 177 passengers have no recorded
age. NaN values cannot be plotted or used in a t-test, so you must remove them
first. `.dropna()` returns a clean Series with only valid numeric age values.

The second line does the exact same thing for non-survivors using `== 0`.

---

## Q4: Print Mean Ages

```python
print(f"Survivors: {len(survived_age)} passengers, mean age: {survived_age.mean():.2f}")
print(f"Not survived: {len(not_survived_age)} passengers, mean age = {not_survived_age.mean():.2f}")
```

**`len(survived_age)`**

`len()` is a built-in Python function that returns the length (number of elements)
of a sequence. Here it counts how many age values remain after dropping NaNs.

**`survived_age.mean()`**

Same `.mean()` as before — computes the average age of survivors. `:.2f` formats
it to 2 decimal places.

---

## Q4: Overlapping Histograms

```python
plt.figure(figsize=(8,5))
plt.hist(survived_age, bins=30, alpha=0.5, color='lightcoral', label='Survived')
plt.hist(not_survived_age, bins=30, alpha=0.5, color='steelblue', label='Not Survived')
plt.title("Age Distribution: Survivors vs Non-Survivors")
plt.xlabel('Age')
plt.ylabel('Number of Passengers')
plt.legend()
plt.tight_layout()
plt.savefig("../visuals/age_distribution.png", dpi=150, bbox_inches='tight')
plt.show()
```

**`plt.hist(survived_age, bins=30, alpha=0.5, color='lightcoral', label='Survived')`**

`plt.hist()` draws a histogram. The first argument is the data — `survived_age`,
your Series of ages. `bins=30` tells matplotlib to divide the full age range into
30 equal-width intervals and count how many ages fall into each interval. More bins
= more detailed but noisier; fewer bins = smoother but less detail. 30 is a
reasonable choice for ~340 values.

`alpha=0.5` controls transparency. Alpha ranges from 0 (completely invisible) to
1 (completely solid). Setting both histograms to 0.5 means where they overlap, you
can see both colors bleeding through each other, making the overlap region visible.

`label='Survived'` assigns a name to this histogram series. The label is not
displayed until you call `plt.legend()`.

**Why call `plt.hist()` twice?**

Calling it twice on the same canvas draws both histograms on top of each other.
Matplotlib does not clear the canvas between `plt.hist()` calls — it keeps adding
to the same figure until you call `plt.show()` or `plt.figure()`.

**`plt.legend()`**

Draws the legend box in the corner of the chart, showing the color and label for
each series. Without this line, the `label=` arguments in `plt.hist()` would be
ignored.

---

## Q4: Two-Sample t-Test

```python
t_stat, p_val = stats.ttest_ind(survived_age, not_survived_age)
print(f"t-statistic: {t_stat:.4f}")
print(f"p-value: {p_val:.4f}")
```

**`stats.ttest_ind(survived_age, not_survived_age)`**

`ttest_ind` stands for t-test for independent samples. `ind` = independent — the
two groups are completely separate people (a survivor cannot also be a non-survivor),
so they are independent samples. This is different from a paired t-test where the
same person is measured twice. The function takes your two age Series, computes
the t-statistic by comparing their means relative to their spread, and returns
both the t-statistic and the p-value.

**`t_stat, p_val =`**

Same tuple unpacking as the chi-square cell — the function returns two values and
you assign both in one line.

---

## Q4: Interpret t-Test

```python
if p_val < 0.05:
    print("Age significantly differs between survivors and non-survivors (p < 0.05).")
else:
    print("No significant age difference found.")
```

Same if-else pattern as Cell 12, now using `p_val` instead of `p`. The logic is
identical — if p-value is below the 0.05 significance threshold, reject the null
hypothesis.

---

## Q5: Check Fare Column Info

```python
print(df[['Fare', 'Survived']].info())
```

**`df[['Fare', 'Survived']]`**

Double square brackets select multiple columns from a DataFrame. Single brackets
`df['Fare']` return a Series (one column). Double brackets `df[['Fare', 'Survived']]`
return a DataFrame (multiple columns). The inner `['Fare', 'Survived']` is a
Python list of column names.

**`.info()`**

A pandas method that prints a summary of the DataFrame — the number of rows,
column names, count of non-null values per column, and data type of each column.
Useful for quickly checking if there are any missing values in these two columns
before analysis.

---

## Q5: Prepare Fare Data

```python
clean_fare = df.dropna(subset='Fare')
survived_fare = clean_fare[clean_fare['Survived'] == 1]['Fare']
not_survived_fare = clean_fare[clean_fare['Survived'] == 0]['Fare']
```

**`df.dropna(subset='Fare')`**

`dropna()` on a DataFrame (not a Series) removes entire rows that have missing
values. Without the `subset` argument it would remove any row with a missing value
in any column — too aggressive. `subset='Fare'` restricts it to only drop rows
where Fare specifically is missing. The result is stored in `clean_fare` — a
DataFrame with guaranteed non-missing Fare values.

**Lines 2 and 3**

Same boolean indexing pattern as Cell 21. Filter `clean_fare` to survivors only,
then extract the Fare column. Repeat for non-survivors. You now have two Series of
fare values ready for plotting.

---

## Q5: Box Plot

```python
plt.figure(figsize=(8,5))
plt.boxplot([survived_fare, not_survived_fare],
            labels=['Survived', 'Not Survived'],
            patch_artist=True,
            boxprops=dict(facecolor='lightblue', color='black'),
            medianprops=dict(color='red'))
plt.yscale('log')
plt.grid(axis='y', linestyle='--', alpha=0.7)
plt.title('Distribution of Fare by Survival')
plt.xlabel('Survival Status')
plt.ylabel('Fare')
plt.xticks(rotation=0)
plt.tight_layout()
plt.savefig("../visuals/fare_boxplot.png", dpi=150, bbox_inches='tight')
plt.show()

median_0 = clean_fare[clean_fare['Survived'] == 0]['Fare'].median()
median_1 = clean_fare[clean_fare['Survived'] == 1]['Fare'].median()
print(f"Median Fare for Not Survived: {median_0:.2f}")
print(f"Median Fare for Survived: {median_1:.2f}")
```

**`plt.boxplot([survived_fare, not_survived_fare], ...)`**

`plt.boxplot()` draws one or more box plots. When you pass a list of two Series,
it draws two side-by-side boxes — one per group. A box plot visualises 5 numbers:
the minimum, Q1 (25th percentile), median (Q2), Q3 (75th percentile), and maximum,
plus dots for outliers beyond 1.5×IQR.

**`labels=['Survived', 'Not Survived']`**

Labels for the x-axis below each box.

**`patch_artist=True`**

By default, matplotlib draws box plots as hollow outlines only. Setting
`patch_artist=True` fills the box with a solid color. Without this, the
`boxprops=dict(facecolor=...)` argument has no effect.

**`boxprops=dict(facecolor='lightblue', color='black')`**

`boxprops` controls the visual style of the box (the rectangle between Q1 and Q3).
`dict(...)` creates a Python dictionary — a collection of key-value pairs.
`facecolor='lightblue'` fills the box with light blue. `color='black'` sets the
border color of the box.

**`medianprops=dict(color='red')`**

`medianprops` controls the style of the median line drawn inside the box. Setting
`color='red'` makes the median line red so it stands out clearly.

**`plt.yscale('log')`**

Sets the y-axis to a logarithmic scale instead of the default linear scale. Fare
data is heavily right-skewed — most passengers paid low fares but a few paid
extremely high fares (outliers up to £500+). On a linear scale, the box for most
passengers would be squashed near the bottom and the outliers would dominate the
chart. A log scale compresses the high end and stretches the low end, making the
boxes readable.

**`plt.grid(axis='y', linestyle='--', alpha=0.7)`**

Adds horizontal reference grid lines. `axis='y'` means only horizontal lines (no
vertical). `linestyle='--'` makes them dashed. `alpha=0.7` makes them slightly
transparent so they do not compete with the data.

**`.median()`**

Same as `.mean()` but computes the median (middle value when sorted). Used here
instead of mean because Fare is skewed — the median is a more robust measure of
the typical fare, less affected by the extreme high-fare outliers.

---

## Q6: Correlation Heatmap

```python
numeric_df = df.select_dtypes(include=[np.number])
numeric_df = numeric_df.drop(columns=['PassengerId'])
numeric_df['Age'] = numeric_df['Age'].fillna(numeric_df['Age'].median())
correlation_matrix = numeric_df.corr()
plt.figure(figsize=(10,8))
sns.heatmap(correlation_matrix, annot=True, cmap='coolwarm',
            fmt='.2f', linewidths=0.5, cbar_kws={'shrink': 0.8},
            center=0, square=True)
plt.title('Correlation Matrix of Numerical Features')
plt.tight_layout()
plt.savefig("../visuals/correlation_matrix.png", dpi=150, bbox_inches='tight')
plt.show()
```

**`df.select_dtypes(include=[np.number])`**

`select_dtypes` filters columns by their data type. `include=[np.number]` means
"keep only columns whose dtype is numeric" — integers and floats. This
automatically excludes text columns like `Name`, `Sex`, `Ticket`, `Cabin`,
`Embarked`. You cannot compute Pearson correlation on text, so this step is
necessary. `np.number` is a numpy type that represents any numeric type.

**`numeric_df.drop(columns=['PassengerId'])`**

`drop()` removes specified columns. `columns=['PassengerId']` specifies which
column to remove. `PassengerId` is numeric (it passed the `select_dtypes` filter)
but it is just a row number — its correlation with anything would be meaningless.
So you drop it before computing correlations.

**`numeric_df['Age'].fillna(numeric_df['Age'].median())`**

`fillna()` replaces NaN values with a specified value. `numeric_df['Age'].median()`
computes the median age of all passengers with known ages. Every passenger whose
age is missing gets replaced with this median value. This is called median imputation
— a simple strategy to handle missing data without removing rows entirely.

Why median and not mean? Because if there are outliers in Age (very old passengers),
the mean gets pulled toward them. The median is the middle value and is unaffected
by outliers, making it a safer imputation choice.

`numeric_df['Age'] =` overwrites the Age column with the imputed version.

**`numeric_df.corr()`**

Computes the Pearson correlation coefficient between every pair of numeric columns
simultaneously. The result is a square DataFrame (a matrix) where both rows and
columns are column names, and each cell contains the correlation coefficient between
that row-column pair. Diagonal values are always 1.0 (a variable is perfectly
correlated with itself).

**`sns.heatmap(correlation_matrix, ...)`**

Seaborn's `heatmap` function draws a grid where each cell is color-coded by its
value. Each argument:

`annot=True` — print the numeric correlation coefficient inside each cell. Without
this, you only see colors with no numbers.

`cmap='coolwarm'` — the color map. `coolwarm` goes from blue (negative correlation)
through white (zero) to red (positive correlation), which is intuitive for
correlation matrices.

`fmt='.2f'` — the format for the annotated numbers inside cells. Same as before:
fixed-point with 2 decimal places. Note: `fmt` is used instead of a format string
inside `{}` because seaborn handles the annotation formatting separately.

`linewidths=0.5` — draws thin lines between cells to visually separate them.

`cbar_kws={'shrink': 0.8}` — `cbar` refers to the color bar (the legend strip
on the side showing what colors mean). `kws` is short for keyword arguments —
a dictionary of settings for the color bar. `'shrink': 0.8` makes the color bar
80% of its default height so it fits neatly next to the square heatmap.

`center=0` — tells seaborn to center the color scale at 0, so white always
represents zero correlation. Without this, seaborn might center at the midpoint of
your data range, which could be off-zero.

`square=True` — forces each cell to be a perfect square. Without this, cells might
be rectangular depending on the figure size.

---

## Q6: Print Correlation Values

```python
print("=== Correlation Analysis Results ===\n")
print("Correlation with Survival:")
print(correlation_matrix['Survived'].sort_values(ascending=False))
print("\n")

corr_values = correlation_matrix.unstack().sort_values(ascending=False)
corr_values = corr_values[corr_values < 0.999]
print("Top Correlations (excluding 1.0):")
print(corr_values.head(10))
print("\n")

print("Top Negative Correlations:")
print(corr_values.tail(10))
```

**`"\n"` inside print strings**

`\n` is an escape sequence for a newline character. Inside a print statement, it
inserts a blank line in the output. So `print("\n")` prints a blank line.

**`correlation_matrix['Survived']`**

Selects the Survived column from the correlation matrix. This gives you a Series
showing how each feature correlates with the target variable Survived.

**`.sort_values(ascending=False)`**

Sorts the Series from highest value to lowest. `ascending=False` means descending
order — highest correlation at the top. This makes it easy to read which features
are most positively correlated with survival.

**`correlation_matrix.unstack()`**

A correlation matrix is a 2D DataFrame (rows and columns are both feature names).
`unstack()` converts it from a 2D matrix into a 1D Series by stacking the row and
column indices into a multi-level index. The result lists every row-column pair as
a single entry. This lets you sort all pairwise correlations in one list rather
than looking at rows and columns separately.

**`corr_values[corr_values < 0.999]`**

Boolean indexing on a Series. Keeps only values less than 0.999 — this filters out
the diagonal values (which are exactly 1.0, representing each variable's correlation
with itself). Using 0.999 instead of 1.0 accounts for potential floating-point
rounding.

**`.head(10)`**

Returns the first 10 rows of a Series or DataFrame. Since the Series is sorted
descending, `.head(10)` gives you the top 10 highest correlations.

**`.tail(10)`**

Returns the last 10 rows. Since the Series is sorted descending, `.tail(10)` gives
you the 10 most negative (strongest inverse) correlations.

---

## Quick Reference — All Unique Patterns in This Notebook

**Data access patterns**

`df['Col']` selects one column and returns a Series. `df[['Col1', 'Col2']]` selects
multiple columns and returns a DataFrame. `df[df['Col'] == value]` filters rows by
a condition (boolean indexing). Chaining them like `df[df['A'] == x]['B']` first
filters rows then extracts a column.

**Aggregation patterns**

`.mean()` computes average. `.median()` computes middle value. `.sum()` adds values.
`df.groupby('Col')['Other'].mean()` splits by one column and computes stats on
another.

**Missing value patterns**

`.isnull().sum()` counts NaNs per column. `.dropna()` removes NaN rows from a
Series. `df.dropna(subset='Col')` removes rows where a specific column is NaN.
`.fillna(value)` replaces NaNs with a value.

**Statistical test patterns**

`pd.crosstab(A, B)` builds an observed frequency table. `stats.chi2_contingency(table)`
runs a chi-square test, returns (chi2, p, dof, expected). `stats.ttest_ind(g1, g2)`
runs a two-sample t-test, returns (t_stat, p_val). Both use tuple unpacking to
assign multiple return values at once.

**Plotting patterns**

`plt.figure(figsize=(...))` always comes first. Charts are built by calling
functions that add layers to the same canvas. `plt.tight_layout()` then
`plt.savefig(...)` then `plt.show()` always come last in that order.

---

*Titanic EDA Project 01 | May 2026*
