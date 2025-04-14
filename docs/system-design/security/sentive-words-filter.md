---
title: Summary of Sensitive Word Filtering Solutions
category: System Design
tag:
  - Security
---

The system needs to filter sensitive words from user input, such as terms related to pornography, politics, and violence.

The commonly used algorithms for sensitive word filtering are the **Trie Tree algorithm** and the **DFA algorithm**.

## Algorithm Implementation

### Trie Tree

The **Trie Tree**, also known as a prefix tree or digital tree, is a variant of a hash tree usually employed for string matching, enabling the efficient lookup of a string within a collection of strings. A keyword suggestion feature in a web browser can be implemented using a Trie Tree.

![Browser Trie Tree Demonstration](https://oss.javaguide.cn/github/javaguide/system-design/security/brower-trie.png)

Assuming our sensitive word database includes the following terms:

- HD Video
- HD CV
- Tokyo Cold
- Tokyo Hot

The Trie Tree constructed for sensitive words looks like this:

![Sensitive Word Trie Tree](https://oss.javaguide.cn/github/javaguide/system-design/security/sensitive-word-trie.png)

When we want to lookup the string "Tokyo Hot", we will break it down into individual characters "东", "京", and "热", and start matching from the root of the Trie Tree.

It is evident that the **core principle of the Trie Tree is quite simple: it enhances string matching efficiency by using common prefixes.**

The library [Apache Commons Collections](https://mvnrepository.com/artifact/org.apache.commons/commons-collections4) has an implementation of the Trie Tree:

![Trie Tree Implementation in Apache Commons Collections](https://oss.javaguide.cn/github/javaguide/system-design/security/common-collections-trie.png)

```java
Trie<String, String> trie = new PatriciaTrie<>();
trie.put("Abigail", "student");
trie.put("Abi", "doctor");
trie.put("Annabel", "teacher");
trie.put("Christina", "student");
trie.put("Chris", "doctor");
Assertions.assertTrue(trie.containsKey("Abigail"));
assertEquals("{Abi=doctor, Abigail=student}", trie.prefixMap("Abi").toString());
assertEquals("{Chris=doctor, Christina=student}", trie.prefixMap("Chr").toString());
```

The Trie Tree is a data structure that trades off memory for time, and it can occupy a substantial amount of memory. For this reason, improved versions of the Trie Tree, such as the Double-Array Trie (DAT), are commonly used in real-world projects.

The designers of the DAT are Aoe Jun-ichi, Mori Akira, and Sato Takuya from Japan, who published a paper in 1989 titled [“An Efficient Implementation of Trie Structures”](https://www.co-ding.com/assets/pdf/dat.pdf), detailing the construction and application of the DAT. The original sample code can be found here: <https://github.com/komiya-atsushi/darts-java/blob/e2986a55e648296cc0a6244ae4a2e457cd89fb82/src/main/java/darts/DoubleArrayTrie.java>. Compared to a Trie Tree, the DAT has significantly lower memory usage, reaching about 1% of the memory required by a Trie Tree. DAT is widely used in Chinese word segmentation, natural language processing, and information retrieval, making it an excellent data structure.

### AC Automaton

The Aho-Corasick (AC) automaton is an improved algorithm built on the Trie Tree, serving as a multi-pattern matching algorithm invented by researchers Alfred V. Aho and Margaret J. Corasick at Bell Labs.

The AC automaton utilizes the Trie Tree to store the prefixes of pattern strings and employs failure matching pointers (mismatch pointers) to handle matching failures. For a detailed introduction to the AC automaton, you can reference this article: [Subway in Ten Minutes | AC Automaton](https://zhuanlan.zhihu.com/p/146369212).

Using DAT to represent the AC automaton can combine the advantages of both to achieve an efficient multi-pattern matching algorithm. There is already an open-source Java implementation on GitHub: <https://github.com/hankcs/AhoCorasickDoubleArrayTrie>.

### DFA

**DFA** (Deterministic Finite Automata) is a type of finite automaton, with its counterpart being NFA (Non-Deterministic Finite Automata).

For a detailed introduction to DFA, refer to this article: [Finite Automaton DFA & NFA (Study Notes) - Xiaosnail's article - Zhihu](https://zhuanlan.zhihu.com/p/30009083).

[Hutool](https://hutool.cn/docs/#/dfa/%E6%A6%82%E8%BF%B0) provides an implementation of the DFA algorithm:

![Hutool's DFA Algorithm](https://oss.javaguide.cn/github/javaguide/system-design/security/hutool-dfa.png)

```java
WordTree wordTree = new WordTree();
wordTree.addWord("大");
wordTree.addWord("大憨憨");
wordTree.addWord("憨憨");
String text = "那人真是个大憨憨！";
// Get the first matching keyword
String matchStr = wordTree.match(text);
System.out.println(matchStr);
// Standard match, matching the shortest keyword and skipping already matched keywords
List<String> matchStrList = wordTree.matchAll(text, -1, false, false);
System.out.println(matchStrList);
// Match the longest keyword, skipping already matched keywords
List<String> matchStrList2 = wordTree.matchAll(text, -1, false, true);
System.out.println(matchStrList2);
```

Output:

```plain
大
[大, 憨憨]
[大, 大憨憨]
```

## Open Source Projects

- [ToolGood.Words](https://github.com/toolgood/ToolGood.Words): A high-performance sensitive word (illegal word/profanity) detection and filtering component, which also supports traditional and simplified Chinese conversion, full-width and half-width conversion, Chinese character to pinyin conversion, fuzzy search, and other features.
- [sensitive-words-filter](https://github.com/hooj0/sensitive-words-filter): A sensitive word filtering project that supports filtering with TTMP, DFA, DAT, hash bucket, and Trie algorithms. It provides interfaces for highlighting, filtering, word judgment, and replacement of text.

## Papers

- [An Automatic Filtering Management System for Sensitive Words](https://patents.google.com/patent/CN101964000B)
- [A Method and System for Filtering Sensitive Words in Online Games](https://patents.google.com/patent/CN103714160A/zh)

<!-- @include: @article-footer.snippet.md -->
