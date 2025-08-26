Goal
===
Leverage AI to recognize patterns in the computational graph of an LLM (for instance, "linear layer", or "self-attention block").

Context
===
Currently, we group nodes based on their names, using a trie. But a lot of nodes don't have meaningful names in the onnx graph, and end up just loose. We want to use AI to group the loose nodes in a meaningful way and explain what the purpose of the group is.

The graph exists in multiple forms: (a) binary onnx file, (b) parsed onnx protocol buffer, (c) React Flow DAG, (d) ELK DAG, etc. We need to grab one of these representations, show it to an LLM, and have it annotate the data structure with meaningful groups.

Some questions we need to figure out:
1. What's the best representation to send to the LLM?
    - Which one is most likely to be understood correctly?
    - Which one is easiest to annotate by the LLM, and most likely to be returned without structural errors?
    - Which one is the most scalable solution? For instance, if we want to support formats other than ONNX, over-indexing on ONNX at this stage will make scaling harder.
2. Should we send the entire graph to the LLM, or only parts of it?
    - Some models can be huge, especially in text format. For instance, the BERT-Base model as an ONNX protocol buffer in text format is over 1GB.
    - The trie method already handles quite a few parts of the graph, so it might be wasteful to send them to an LLM.
3. How should we prompt the LLM? What sort of examples should we provide?
4. What sort of output should we expect from the LLM? Do LLM providers support output data structure specifications?