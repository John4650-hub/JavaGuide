I don't...
type: summary of sensitive word filtering schemes
Category: System design
Tag:
- Clear.
I don't...

The system requires that user-inputted text be filtered for sensitive words such as pornography, politics, and violence-related vocabulary.

More sensitive word filters use the **Trie tree algorithm** and **DFA algorithm**.

Algorithm

# Trie tree #

**Trie Tree**, also known as Dictionary Tree or Word Search Tree, is a variant of Hash Tree and is usually used for string matching to solve the problem of quickly finding a string in a string set. Keyword tips like those in a browser can be made using a Trie Tree.

![Browser Trie Tree Effects Presentation](https://oss.javaguide.cn/github/javaguide/system-desent/security/brower-trie.png)

If we have the following sensitive words in our sensibilities:

- HD video
- HD CV
- Tokyo cold
- Tokyo fever

This is what the tree looks like:

![Sensitive word Trie Tree](https://oss.javaguide.cn/github/javaguide/system-design/security/sentive-word-trie.png)

When we look for the corresponding string "Tokyo Thermal," we cut the string into single characters "To," "kyo," "Thermal," and then we start to match the root node of the Trie tree.

As can be seen, the **Trie tree's core principle is really simple: to improve string matching efficiency through a common prefix.**

This library includes Trie Tree:

![Common Collections Trie](https://oss.javaguide.cn/github/javaguide/system-design/security/common-collections-trie.png)

```Java
Trie<String, String> trie = new PatriciaTrie();
trie.put("Abigail", "student");
trie.put("Abi", "doctor");
trie.put("Chris", "doctor");
trie.put("Christina", "student");
Assert.assertTrue(trie.containsKey("Abigail"));
Assert.assertEquals("Abi=doctor, Abigail=student", trie.prefixMap("Abi").toString());
Assert.assertEquals("Chris=doctor, Christina=student", trie.prefixMap("Chr").toString());
```

Trie Tree is a data structure that uses space for time, with larger RAMs. It is precisely for this reason that an improved version of Trie trees, such as the double-array Trie Tree (Double-Array Trie, DAT), is used in actual projects.

The designers of DAT are Aoe Jun-ichi, Mori Akira, and Sato Takuya of Japan, who published a paper in 1989 [An Economic Integration of Trie Structures](https://www.co-ding.com/assets/pdf/dat.pdf) detailing the construction and application of DAT. The original author's sample code address is: <https://github.com/komiya-atsushi/darts-java/blob/e2986a55e648296cccca6244a4a2e4a257cd89fb82/src/main/va/darts/DoubleArayTrie.java>. Compared to the Trie tree, the memory of DAT is extremely low, reaching about 1% of Trie tree memory. DAT is an excellent data structure with extensive applications in the areas of Chinese dictionaries, natural language processing, and information retrieval.

# AC automaton #

Aho-Corasick (AC) automaton is an improved algorithm based on the Trie tree, a multi-pattern matching algorithm invented by Alfred V. Aho and Margaret J. Corasick, researchers at Bell Laboratories.

AC automaton algorithms use the Trie tree to store the prefix of a pattern string to handle a failed jump by failing to match a pointer (defunct pointer). For a detailed account of AC's automaton, the article can be viewed: [Ten Minutes of Subway AC's Automaton](https://zhuanlan.zhihu.com/p/1463692122).

If you use the DAT mentioned above as an AC automaton, you can have an efficient multi-pattern matching algorithm. Open source Java is available on GitHub: <https://github.com/hankcs/AhoCorasickDoubleArrayTrie>.

# DFA #

**DFA** (Deterministic Finite Automaton) identifies the existence of a pattern, which corresponds to the NFA (Non-Deterministic Finite Automaton, which is not certain of its own pattern).

A detailed description of the DFA can be found in this article: [Positively Motivated DFA & NFA (Learning Notes) - Snail Articles - Knowledge](https://zhuanlan.zhihu.com/p/390009083).

[Hutool](https://hutool.cn/docs/#/dfa/%E6%A6%A8%E8%AF%80) provides the DFA algorithm for realization:

![Hutool DFA](https://oss.javaguide.cn/github/javaguide/system-design/security/hutool-dfa.png)

```Java
WordTree wordTree = new WordTree();
wordTree.addWord("example");
wordTree.addWord("sample");
wordTree.addWord("test");
String text