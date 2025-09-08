sigmoid - function to squish values to 0 - 1 range (ak logistic curve)
negative closer to zero
position closer to 1
0 = 0.5
ReLU - most common activation function in neural networks.
outputs 0 for negative inputs and the input value itself for positive inputs.
Formula: f(x) = max(0, x)

NN - neural network

1. pre-training - feed massive data to tune parameters
   - parameters / weights - initially random
   - uses backpropagation
2. RLHF - reinforcement learning with human feedback
   humans flag answers as unhelpful

GPUs run operations in parallel

chatbot:

- start with some intructions describing conversation
- add on the user's question
- feed into the LLM to predict next word
  - don't always choose most likely word, to create randomness
- repeatedly feed whole conversation to LLM
  - before 2017 this was done sequentially

2017 google created "transformer"

- all input words of input are processed in parallel
- meaning of word is converted to a number
- attention: each word number is changed depending on context (words surrounding it)
  - e.g. bank could have different meanings depending on context
- feedforward NN encodes extra meaning to all attention values repeatedly to add value
- use last word value in final iteration to predict next word
- it's very difficult to know why an LLM chose it's final answer
