# node-red-discrete-fsm
## Finite state machine nodes for Node-RED
Node-RED FSM that exposes inputs, conditions, states, and outputs as discrete nodes.

Inputs and outputs can be boolean, numeric, or string type. 

Conditions are named input vectors that are specified using a mathjs expression.

States are named output vectors.

Transitions are defined "out of" each state, by a condition and a corresponding next state.

There are global conditions, defined over all states, and local conditions, defined separately within the scope of each state.

Input nodes accept messages containing input values in their payload. The input node formats the input value into a well-defined message format with a topic of "input" and containing a key value pair with the name of the input as the key. THe input name defaults to the node name.

Global condition nodes receive all input messages and keep a cache of the most recent updates. The condition is stored as a mathjs expression that contains input names as variables, and is evaluated either whenever an input message is received, or when a sync message is received. When a condition is evaluated true, a "condition" message is output to the state nodes.

State nodes receive consdition messages and evaluate them against a stored transition table. When a condition match is found, a "transition" message is sent containing the next state. This message is fed back to the state nodes so the next state can be selected. Each state node has an associated output vector that is applied to the output nodes when a state transition occurs to that state. 

Thus, a state transition involves as many as four messages, an input message to be evaluated by condition nodes, a condition message that triggers the state transition, a transition message that selects the next state, and an "output" message is generated by the state node and recieved by the output nodes. 

State nodes may optionally have local conditions that are evaluated directly on input messages. When a local condition evaluates true, the transition table is checked locally without a condition message being generated. Both global conditions and local conditions are optional, but there needs to be at least one condition defined somewhere for the state machine to switch states.

Output nodes receive output messages, which can contain many output values, and select the output value corresponding to the node name for outgoing messages. 

The nodes are connected in a multicast topology through junctions. All input nodes are connected to all condition nodes. All condition nodes are connected to all state nodes, and all state nodes are connected to all output nodes. There is a feedback path from all state node outputs back to all state node inputs, to pass transition messages.

The nodes are self-contained, that is they don't rely on flow context or a configuration node to group them into any particular state machine instance. This allows the construction of composite and coupled state machines which may share inputs, conditions, and outputs, and may have a plurality of active states.

