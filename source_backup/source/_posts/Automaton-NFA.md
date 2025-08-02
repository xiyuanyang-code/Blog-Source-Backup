---
title: Automaton NFA
date: 2025-05-05 19:15:24
index_img: /img/cover/NFA.jpg
excerpt: Nondeterministic finite automaton
math: true
categories:
  - Code
tags:
  - NFA
  - C/C++
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# 非确定有限状态自动机

第四次机考考到了NFA，笔者在做的时候题目完全没有看懂（甚至都不知道什么叫自动机），细细研究下来发现竟然挺好玩，遂开个坑。

[这个是题源](https://acm.sjtu.edu.cn/OnlineJudge/problem/2684)

## Preliminaries

在开始今天正题之前，我们需要了解几个预备的基础知识：

- **自动机**
- **有限状态自动机**
- **确定 & 非确定 有限状态自动机**

### Automaton

自动机是一个对**信号序列**进行**判定**的数学模型。例如我们需要判断一个字符串（信号序列）是否是一个合法的电子邮件地址，就可以建模成自动机的形式进行形式化的判断。而在自动机中，**采用图论的形式**进行判定。

{% note primary %}

下面的内容节选自维基百科[^2]：

**自动机**可以表示为**5-元组** $⟨Q,Σ,δ,q_0,F⟩$，这里的:

- $Q$ 是**状态**的集合。
- $∑$ 是符号的有限集合，我们称为这个自动机接受的语言的**字母表**。
- $δ$ 是**转移函数**，就是:

$$\delta: Q \times \sum \to Q$$

- $q_0$ 是**开始状态**，就是说自动机在还未处理输入的时候的状态(明显的 $q_0∈ Q$)。
- $F$ 是叫做**终止状态**的 Q 中的状态的集合(就是 $F⊆Q$)。

{% endnote %}

#### 举一个例子

这个定义也太抽象了！举一个具体一点的例子，假设给定一个**有限长**字符序列$t$，需要判断这个字符序列是否包含连续的子串$t_i =$ "ab"。$\sum$可以理解为一个**domain**，即26个英文字母（对英文字符序列而言）。

那我们的目标就是设计一个自动机，接受所有包含子串 "ab" 的字符串，这样我们输入待判断的字符序列之后就可以判断其能否被这个自动机所接受。

什么叫**状态的集合**？可以理解为在栈模拟递归中的**栈帧空间**的状态描述。例如在这个问题中有三个状态：

- 未读取到有效字符（当前字符既不是a也不是b）
- 已经读取到了`a`，但是还没有匹配到`ab`
- `ab`读取成功，terminated（即**终止状态**）

$$Q = \\\{ q_0, q_1, q_2 \\\}$$

对于转移函数，就是从$Q$中的一个状态出发，选择一个字符，走到下一个状态。（可以定义为mapping）在这个问题中就是从当前状态出发，输入一个字符（例如我输入的是a），并更新当前的状态（例如我可能会从$q_1$更新到$q_0$）。

这里终止状态只有一种状态，因此$F$就是单元素集。

我需要给定一个初始状态和初始输入，判断**是否存在一种走法**使其最后可以走到终止状态，就认定其**通过了自动机的判定**！

>  换句话说，在自动机接收了所有字符后，停在了一个状态集 $D$ 上，若 $D∪F≠∅$, 那么我们就称这个字符串被 自动机 接受。

在上面的例子中，我们可以画出如下的自动机：

![自动机](https://s1.imagehub.cc/images/2025/05/05/cc9957b285717332082f518d793e5b85.jpg)

从初始状态出发，能否找到一条路径通向终点？这可以转化成**图搜索问题**来解决。因此**自动机设计的关键**在于明确五元组，并画出这样的图。

> 从数学的角度思考，这貌似只是函数的mapping定义的可视化。

### FSA

在绝大多数情况下，自动机研究的问题都限制在**有限**的情况下，即**状态集合是有限的集合**。

### NFA and DFA

在上面的例子中，我们认为**转移函数是一个函数**，换句话说，**给定当前状态和输入符号下**，更新后的状态是唯一的。（例如上面的例子就是）。但是在有些情况下并不是这样，例如**正则表达式**，因此把这种**一对多不确定的情况**称为**非确定有限状态自动机**（NFA），而确定的就叫做确定有限状态自动机（DFA）。

> 在形式理论中可以证明它们是等价的；就是说，对于任何给定NFA，都可以构造一个等价的DFA，反之亦然。

## Problems to solve

我们需要实现一个**简单版本的**正则表达式匹配器。

{% note primary %}

给定一个由 `a` `b` 组成的字符串，希望你判断这些字符串是否符合一定的模式。具体而言，除去字符之外，我们有这些符号：

- `+`. 如 `a+` 表示可以匹配一次或多次 `a`.
- `*`. 如 `b*` 表示可以匹配零次或多次 `b`.
- `?`. 如 `a?` 表示可以匹配零次或一次 `a`.
- 字符串之间可以连接。如 `ab+a*` 代表先匹配 `a`，再匹配 `b+`, 再匹配 `a*`.
- `|`. 如 `a+b|ba*` 代表既可以匹配 `a+b`，也可以匹配 `ba*`. 该运算的优先级是**最低的**。
- 只存在上面的运算优先级，表达式不包含括号。
- 不考虑空字符串的特殊情况。

{% endnote %}

## Explanation

很显然，这是一个**NFA**问题，因为更新后状态是不确定的。

### Several Classes

```cpp
enum class TransitionType { Epsilon,
                                a,
                                b };

struct Transition {
    TransitionType type;
    int to;
    Transition(TransitionType type, int to) : type(type), to(to) {}
};
```

`Transition`结构体代表每一条边，即边的类型和指向的状态终点。

> 这里只考虑静态自动机的设计，因此起点信息将会储存在`NFA`的类中。

```cpp
class NFA {
private:
  /*!
    \brief This is used to store the start state of the NFA.
  */
  int start;
  /*!
    \brief This is used to store the end state of the NFA.
  */
  std::unordered_set<int> ends;
  /*!
    \brief This is used to store the transitions of the NFA.
    \details For example, transitions[3] stores all the transitions beginning
    with \details state 3.
  */
  std::vector<std::vector<Transition>> transitions;
```

我们最终的接口实现是在函数：

```cpp
bool IsAccepted(int state) const { return ends.count(state) > 0; }
```

`ends`是终止状态的集合，给出一个`state`，判断其是否在这个集合中。

### 转移函数的设计

设计自动机的最关键一步就是**设计转移函数**，这样就可以链接不同的节点。

我们需要设计函数：`std::unordered_set<int> Advance(std::unordered_set<int> current_states, char character) const{}`，代表当前状态集合`current_states`接受新字符之后返回新的状态集合。

由于这里有$\epsilon$空转移的存在，我们首先需要处理**空转移**，因为空转移是不消耗任何字符的。我们的工作流如下：

- 我们需要从状态$S$出发，探索**所有只包含空转移**的做法，更新为$S \subseteq\bar{S}$。
- 探索**非空转移**的做法，更新集合$\bar{S} \subseteq D$
- 再次从状态$D$，做一次空转移的探索，更新集合$D \subseteq \bar{D}$，返回集合$\bar{D}$。

具体函数是这样的：

```cpp
std::unordered_set<int> Advance(std::unordered_set<int> current_states, char character) const {
    current_states = GetEpsilonClosure(current_states);
    TransitionType type = (character == 'a') ? TransitionType::a : TransitionType::b;
    std::unordered_set<int> next_states;
    for (int state : current_states) {
        for (const Transition &t : transitions[state]) {
            if (t.type == type) {
                next_states.insert(t.to);
            }
        }
    }
    return GetEpsilonClosure(next_states);
}
```

#### 空转移

如何实现这个函数，使用**队列**。

对于当前状态下的每一个`state`，首先$\bar{S}$需要返回他们本身（因为**我们可以完全不走任何的空转移**），即插入每一个当前状态，如果插入成功，我们需要将对应的状态压入一个队列。

```cpp
std::unordered_set<int> GetEpsilonClosure(std::unordered_set<int> states) const {
    std::unordered_set<int> closure;
    std::queue<int> queue;
    for (int state : states) {
        if (closure.insert(state).second) {
            queue.push(state);
        }
    }
    while (!queue.empty()) {
        int current = queue.front();
        queue.pop();
        for (const Transition &t : transitions[current]) {
            if (t.type == TransitionType::Epsilon && closure.insert(t.to).second) {
                queue.push(t.to);
            }
        }
    }
    return closure;
}
```

#### 其他转移函数

整个类最关键的部分。为了简单，我们直接画图：

> todo



## Codes

```cpp
#include <queue>
#include <stdexcept>
#include <string>
#include <unordered_set>
#include <vector>

namespace Grammar {

    class NFA;
    NFA MakeStar(const char &character);
    NFA MakePlus(const char &character);
    NFA MakeQuestion(const char &character);
    NFA Concatenate(const NFA &nfa1, const NFA &nfa2);
    NFA Union(const NFA &nfa1, const NFA &nfa2);
    NFA MakeSimple(const char &character);

    enum class TransitionType { Epsilon,
                                a,
                                b };

    struct Transition {
        TransitionType type;
        int to;
        Transition(TransitionType type, int to) : type(type), to(to) {}
    };

    class NFA {
        friend char extract_char_from_atom(const NFA &atom);

    private:
        int start;
        std::unordered_set<int> ends;
    public:
        std::vector<std::vector<Transition>> transitions;

    public:
        NFA() = default;
        ~NFA() = default;

        std::unordered_set<int> GetEpsilonClosure(std::unordered_set<int> states) const {
            std::unordered_set<int> closure;
            std::queue<int> queue;
            for (int state : states) {
                if (closure.insert(state).second) {
                    queue.push(state);
                }
            }
            while (!queue.empty()) {
                int current = queue.front();
                queue.pop();
                for (const Transition &t : transitions[current]) {
                    if (t.type == TransitionType::Epsilon && closure.insert(t.to).second) {
                        queue.push(t.to);
                    }
                }
            }
            return closure;
        }

        std::unordered_set<int> Advance(std::unordered_set<int> current_states, char character) const {
            current_states = GetEpsilonClosure(current_states);
            TransitionType type = (character == 'a') ? TransitionType::a : TransitionType::b;
            std::unordered_set<int> next_states;
            for (int state : current_states) {
                for (const Transition &t : transitions[state]) {
                    if (t.type == type) {
                        next_states.insert(t.to);
                    }
                }
            }
            return GetEpsilonClosure(next_states);
        }

        bool IsAccepted(int state) const { return ends.count(state) > 0; }
        int GetStart() const { return start; }

        friend NFA MakeStar(const char &character);
        friend NFA MakePlus(const char &character);
        friend NFA MakeQuestion(const char &character);
        friend NFA MakeSimple(const char &character);
        friend NFA Concatenate(const NFA &nfa1, const NFA &nfa2);
        friend NFA Union(const NFA &nfa1, const NFA &nfa2);
    };

    class RegexChecker {
    private:
        NFA nfa;

        NFA parse_expression(const std::string &regex, size_t &pos) {
            NFA left = parse_term(regex, pos);
            while (pos < regex.size() && regex[pos] == '|') {
                pos++;
                NFA right = parse_term(regex, pos);
                left = Union(left, right);
            }
            return left;
        }

        NFA parse_term(const std::string &regex, size_t &pos) {
            NFA left = parse_factor(regex, pos);
            while (pos < regex.size() && regex[pos] != '|' && regex[pos] != ')') {
                NFA right = parse_factor(regex, pos);
                left = Concatenate(left, right);
            }
            return left;
        }

        NFA parse_factor(const std::string &regex, size_t &pos) {
            NFA atom = parse_atom(regex, pos);
            while (pos < regex.size() && (regex[pos] == '*' || regex[pos] == '+' || regex[pos] == '?')) {
                char op = regex[pos++];
                char c = extract_char_from_atom(atom);
                switch (op) {
                    case '*':
                        atom = MakeStar(c);
                        break;
                    case '+':
                        atom = MakePlus(c);
                        break;
                    case '?':
                        atom = MakeQuestion(c);
                        break;
                    default:
                        throw std::runtime_error("Unexpected operator");
                }
            }
            return atom;
        }

        NFA parse_atom(const std::string &regex, size_t &pos) {
            if (pos >= regex.size()) throw std::runtime_error("Unexpected end of regex");
            char c = regex[pos++];
            if (c != 'a' && c != 'b') throw std::runtime_error("Invalid character in regex");
            return MakeSimple(c);
        }

        char extract_char_from_atom(const NFA &atom) {
            if (atom.transitions.size() < 1 || atom.transitions[0].size() != 1)
                throw std::runtime_error("Invalid atom for closure");
            TransitionType type = atom.transitions[0][0].type;
            if (type != TransitionType::a && type != TransitionType::b)
                throw std::runtime_error("Invalid transition type in atom");
            return (type == TransitionType::a) ? 'a' : 'b';
        }

    public:
        RegexChecker(const std::string &regex) {
            size_t pos = 0;
            try {
                nfa = parse_expression(regex, pos);
                if (pos != regex.size())
                    throw std::runtime_error("Invalid regex");
            } catch (const std::exception &e) {
                throw std::runtime_error("Invalid regex: " + std::string(e.what()));
            }
        }

        bool Check(const std::string &str) const {
            if (str.empty()) return false;
            std::unordered_set<int> current = {nfa.GetStart()};
            current = nfa.GetEpsilonClosure(current);
            for (char c : str) {
                current = nfa.Advance(current, c);
                if (current.empty()) return false;
            }
            for (int state : current) {
                if (nfa.IsAccepted(state)) return true;
            }
            return false;
        }
    };

    NFA MakeStar(const char &character) {
        NFA nfa;
        nfa.start = 0;
        nfa.ends.insert(0);
        nfa.transitions.resize(1);
        TransitionType type = (character == 'a') ? TransitionType::a : TransitionType::b;
        nfa.transitions[0].emplace_back(type, 0);
        nfa.transitions[0].emplace_back(TransitionType::Epsilon, 0);
        return nfa;
    }

    NFA MakePlus(const char &character) {
        NFA simple = MakeSimple(character);
        simple.transitions[1].emplace_back(TransitionType::Epsilon, 0);
        return simple;
    }

    NFA MakeQuestion(const char &character) {
        NFA nfa;
        nfa.start = 0;
        nfa.ends.insert(1);
        nfa.transitions.resize(2);
        TransitionType type = (character == 'a') ? TransitionType::a : TransitionType::b;
        nfa.transitions[0].emplace_back(type, 1);
        nfa.transitions[0].emplace_back(TransitionType::Epsilon, 1);
        return nfa;
    }

    NFA Concatenate(const NFA &nfa1, const NFA &nfa2) {
        NFA result;
        int m = nfa1.transitions.size();
        result.start = nfa1.start;
        result.transitions = nfa1.transitions;
        for (const auto &trans : nfa2.transitions) {
            std::vector<Transition> adjusted;
            for (const Transition &t : trans) {
                adjusted.emplace_back(t.type, t.to + m);
            }
            result.transitions.push_back(adjusted);
        }
        for (int end : nfa1.ends) {
            result.transitions[end].emplace_back(TransitionType::Epsilon, nfa2.start + m);
        }
        for (int end : nfa2.ends) {
            result.ends.insert(end + m);
        }
        return result;
    }

    NFA Union(const NFA &nfa1, const NFA &nfa2) {
        NFA result;
        int m = nfa1.transitions.size();
        int n = nfa2.transitions.size();
        result.transitions.resize(1 + m + n + 1);
        result.start = 0;
        result.ends.insert(1 + m + n);

        result.transitions[0].emplace_back(TransitionType::Epsilon, nfa1.start + 1);
        result.transitions[0].emplace_back(TransitionType::Epsilon, nfa2.start + 1 + m);

        for (int i = 0; i < m; ++i) {
            for (const Transition &t : nfa1.transitions[i]) {
                result.transitions[i + 1].emplace_back(t.type, t.to + 1);
            }
        }
        for (int i = 0; i < n; ++i) {
            for (const Transition &t : nfa2.transitions[i]) {
                result.transitions[i + m + 1].emplace_back(t.type, t.to + m + 1);
            }
        }
        for (int end : nfa1.ends) {
            result.transitions[end + 1].emplace_back(TransitionType::Epsilon, 1 + m + n);
        }
        for (int end : nfa2.ends) {
            result.transitions[end + m + 1].emplace_back(TransitionType::Epsilon, 1 + m + n);
        }
        return result;
    }

    NFA MakeSimple(const char &character) {
        NFA nfa;
        nfa.start = 0;
        nfa.ends.insert(1);
        nfa.transitions.resize(2);
        TransitionType type = (character == 'a') ? TransitionType::a : TransitionType::b;
        nfa.transitions[0].emplace_back(type, 1);
        return nfa;
    }

}// namespace Grammar
```

> 谢谢deepseek老师，真的太厉害了。

## References

[^1]: https://acm.sjtu.edu.cn/OnlineJudge/problem/2684
[^2]: https://zh.wikipedia.org/wiki/%E8%87%AA%E5%8B%95%E6%A9%9F%E7%90%86%E8%AB%96
