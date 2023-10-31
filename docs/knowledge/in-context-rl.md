## Main idea
!!! warning
    Since the lack of the prerequisite knowledge, I only understand and explain in intuitive way. Can't guarantee absolute correctness.

There are two keypoints in traditional reinforceemnt learning:

* Award and punish mechanism.

* Interaction between the external environment and AI agent.

During the interaction between agent and external environment, the agent get award or punishment in accordance with the mechanism above. 
So that the agent can update and modify the model parameters by backpropagaiton in order to get more award and less punishment next time.

But the In-Context Reinforcement Learning is much more different, it does't using backpropagation to update the model. It use ==**text guidance**==.

!!! example
    We want to calculate the sum of two very complex number $A + B$ , but the model maybe gives the wrong answer at beginning. 
    Rather than directly "punish" the model, we explain the model the detailed right way to calculate the expression. 
    Such as caculte from the lowest significant bit to the highest and decimal/binary rules step by step. 
    After that we ask the model to follow those steps to calculate.

## Reference

!!! quote
    <https://arxiv.org/abs/2210.14215>
