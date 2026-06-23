# 非言語

## 推論

### 正誤
- Read the context carefully
- Have to just use logic to determine which answer is right

### 整数
- This is algebra, but each unit is an integer
- There is an exact way to split, so just use algebra
- In the event where there are possibilities (set number of choices), just brute force it

### 割合
- Just substitute with real numbers
- Read the question carefully in the event they want to merge some entity that isn't initially mentioned

### 対戦
- Make a table immediately. When one loses, make sure to record the win on the opposite side
- There are differences between tournaments, round-robins, and match history
    - Tournaments should use a tournament structure (bracket format)
    - Round robins should use the match table with wins and losses
    - Match history should use the table with faced vs did not face

### 平均
- Always think about the total value of the sums
- If two integers add up to an odd value, the values must be different
- You will get questions where (3 are equal, 2 are completely different)

### 位置関係
- Literally just draw all possibilities

### 順序
- Always keep track of the order with writing
- Common usecase is "There are 4 people with 2 outcomes and no consecutive runs"


## 場合の数
### 順例・組み合わせ
- Permutation is used when order matters (a password, or a race finish)
- Combination is used on simple groups of items
- "A group of 7 has to split into 3 and 4, how many ways to split?" -> 7C3 = 7C4
- "A group of 10 has to split into 3, 3, and 4" -> 10C3 x 7C3

### カード・コイン・サイコロ
- Permutations and combinations
- Nothing else to it

### 席・位置決め
- You have 6 seats. How many ways can you seat 5 people? (6P5)
- Most likely permutations
- If you have a "mirrored arrangements don't count", just divide the final result by 2

### 重複・円・応用
- How many integers can you make with 0, 1, 2 (reusing is allowed) = 2 x 3 x 3 = 18
- If you seat 4 people at a circular table, the number of configurations is (4-1)! = 6
- If you have 2 gold, 2 silver, and 2 bronze medals and had to select 3:
    - Case where all colours are different: 1
    - Case where 2 colours are the same: 3P2 = 6
    - Total = 7
- If you have 2 gold, 2 silver, and 2 bronze medals and had to select 4:
    - Fuck it, just do it manually

## 割合
- 25% of those who chose Japanese food for lunch chose Korean food for dinner
    - You can designate the Japanese Lunch + Korean Dinner as 0.25x of the total x
    - Where x is the total number of people who chose a Japanese Lunch
- These questions just need a lot of algebra and reading omg

## 集合
- In a normal venn diagram, you can subtract A + B - AandB to get the total
- Draw all the venn diagrams!

## 確率
- Probability = Event Space / Sample Space
- Remember to find the total sample space, then write down the event space
- If the question says "At least one red ball" or similar, do 1-(case of no red ball)
- You can use combinations to find the event and sample space

## 金額計算
### 支払い
X、Y、Zの3人が旅行をした。Xが代表して宿の予約と支払いを行い、27000円を支払った。Yは3人分の食費18000円を支払った。Zは3人分の交通費を支払った。後日3人が同額ずつ負担するために、YはXに1400円、ZはXに6200円支払った。

交通費はいくらだったか。

- To find the amount each person paid, we can look at one person. Y paid 18000円 and still had to pay X 1400円. So, Y paid 19400円 in total
- Hence, each person should have paid 19,400円. You can arrive at the same result with 27000 - 1400 - 6200
- Thus, Z paid 19400 - 6200 = 13,200円

甲は乙に3000円、丙に2000円の借金をしており、乙は丙に500円の借金をしている。ある日遊びに行った際に、甲が全員の昼食代総額4500円を代表して払い、乙が全員の神社の入場料総額1500円を払った。これらの代金は甲、乙、丙で均等に負担することにした。

3人の貸し借りがすべてなくなるようにするために、甲は乙に（ア）円、丙に（イ）円をそれぞれ渡せばよい。

このとき（ア）と（イ）に入る数字の組み合わせとして正しいものを選べ。

- When it comes to borrowing and lending money, treat them as negatives and positives respectively.
甲: -5000 + 4500        = -500
乙: 3000 - 500 + 1500   = 4000
丙: 2000 + 500          = 2500
The end goal is to have everyone at 6000/3=2000, so 乙->甲=2000, 丙->甲=500

### 料金・価格
- Sometimes trial and error
- Sometimes you need to do 200-x when figuring out how many people above 200 came

### 損益
ある商品に、原価の4割の利益が出るように定価を設定した。
この商品を定価の2割引で売った時、120円の利益となった。
このとき原価はいくらか。


