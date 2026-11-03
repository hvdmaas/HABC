# HABC model: a plain-language guide to the settings

This guide explains in beginner-friendly terms what every button, slider, chooser and plot in the HABC NetLogo model does. 

## Description

The model simulates a group of people (the "agents") in a social network that interact with neighbours and form opinions about one topic.

The model extends the Hierarchical Ising Opinion Model (HIOM). Its central idea is that **attention changes how opinions behave**. People with lower attention have opinions that are more easily swayed by information. People with higher attention tend to have opinions more resistant to change. A society where everyone has high attention can therefore split everyone into two extremes, i.e., polarisation.

HABC adds **caution**. In the Blume-Capel version of the model, being neutral is a real option, and caution sets how attractive that option is. The question the HABC presets explore is whether raising caution can pull a polarised society back towards the middle.



## Agent variables

Each agent has its own values for:
- opinion: Its current position on the topic: negative, positive, or neutral.
- information: A value that enters the opinion-update equation and can change through interactions, decay, and noise.
- attention: Controls the probability of initiating an interaction and enters the information-exchange and opinion-update equations.
- caution: In the Blume-Capel model, strengthens the tendency towards a neutral opinion.
- local variance: The variance of opinions across the agent and its direct neighbours, calculated in the Blume-Capel model.

## Usage

Choose the general baseline (setup-default), one of the named scenarios (see Presets section), or a custom configuration. Use the single-step go button to advance one tick, or the go button with loop arrows to run continuously. Instructions for Preset-specific interventions appear under their respective section.

Before starting a new run, always click **Clear all**. This clears the world and any Preset-specific behavior, but be aware it preserves any sliders and choosers that are not overwritten by a Preset (if one is chosen). 

- **setup-default**: Clears the world, applies a general baseline configuration and initializes the agents. It overwrites the init settings it explicitly assigns. It does not reset every control: model, nr-nodes, p-connect, topology and caution settings retain their current values unless changed elsewhere.

- **setup**: Builds the network of agents and gives them starting values from the sliders. 
- **Go (Continous)**: Runs the simulation continuously until you press it again.
- **Go (Single Step)**: Runs exactly one tick. Useful for watching what happens step by step.
- **Clear all**: Clears the world, plots and automatic scenario ramps. Sliders and choosers keep their current values.





### Model: how opinions update

**model** (cusp, MF Ising or MF Blume Capel; default MF Blume Capel) sets the equation used to update each agent's opinion each tick.

- **cusp**: the original HIOM rule, based on catastrophe theory. Above an attention of about 0.5, a person can get "stuck" on one side even when their information shifts a bit the other way. They only flip once the information has moved a long way.

- **MF Ising**: a rule borrowed from the physics of magnets (the "mean-field Ising model"). Opinions are pulled towards -1 or +1.

- **MF Blume Capel**: like the Ising rule, but people now have three natural positions: against (-1), neutral (0) and for (+1). Caution sets how attractive neutral is. With low caution, people pick a side at the same attention level as in the Ising rule. With high caution, people drift towards neutral even when attention is high. This is the model the HABC presets are built around.

### Network: who talks to whom

- **topology** (Lattice, Erdős-Rényi or Preferential-attachment; default Lattice): The shape of the social network.
 **Lattice**: a square grid where each person talks to the 4 people directly next to them (up, down, left, right). The grid wraps around at the edges, so people on the border also have 4 neighbours. 
**Erdős-Rényi**: a random network controlled by `p-connect`. 
**Preferential-attachment**: each person links to one random other person, which gives a sparse, loosely connected network. Despite the name, it does not build a true preferential-attachment (hub-and-spoke) network.

- **nr-nodes** (2 to 500, default 1,521): Number of people. It is ignored for Lattice, which always has 39 × 39 = 1,521 people, and capped at 500 for the other two networks.

- **p-connect** (0 to 1, default 0.06): Only used by Erdős-Rényi. The chance that any two people are connected. Higher values give a denser network where everyone has more contacts.

### Attention

- **%active_agents** (0 to 100, default 80): The target share of agents initiating interactions. The model constantly adjusts how fast attention fades to aim for this target: if too many agents are active, attention fades faster, and if too few agents are active, attention fades slower or stops fading. This automated adjustment cannot raise attention, though, so when attention is low enough, the actual percentage of active agents stays below the target. The **%active agents** monitor on the right shows the actual share.
- **da** (0 to 0.4, default 0.02): How much attention increases when two agents interact.
- **mean-init-attention** (0 to 1, default 0.5): The average attention have at the start of the simulation.
- **sd-init-attention** (0 to 0.2, default 0): How much attention varies between agents at the start of the simulation. At 0, all agents start with exactly the same attention.

### Information

When two agents interact, both update their information using the same rule. An agent with higher attention relative to its partner retains more of its own information; an agent with lower attention moves more towards its partner’s information. Both comparisons use attention after the interaction’s attention increase. Both information updates use the information values from before the exchange.


- **rmin** (0 to 1, default 0): The minimum share of a given agent's own information retained after a conversation. At 0, either agent can have its information almost completely replaced. At 1, it prevents changes through information exchange; decay and noise can still change information.
- **p** (0 to 5, default 3): How strongly attention differences affect both agents’ information weighting. When `p` = 0, attention does not affect the weighting of information exchanged. If `rmin` is also 0, two interacting agents whose opinions are within the confidence bound (see `Opinion` section) both adopt the midpoint of their information values.
- **decay_I** (0 to 1, default 1): How much information survives each tick. At 1, nothing fades. Below 1, information fades towards 0. The lower the value, the faster it fades.
- **m-noise-information** (-0.25 to 0.25, default 0): The mean change added to each agent’s information each tick. Positive values bias information in the “for” direction; negative values bias it towards “against”. 
- **sd-noise-information** (0 to 0.2, default 0): The standard deviation of the random change added to each agent’s information each tick. Higher values produce more variation between agents and over time.
- **mean-init-information** (-1 to 1, default 0): The average information people start with.
- **sd-init-information** (0 to 0.5, default 0.2): How much starting information varies between agents.

### Opinion

- **sd-noise-opinion** (0 to 0.1, default 0.001): The standard deviation of the random jitter added to each agent’s opinion every tick. Noise can occasionally cause an agent to leave a stable opinion state and shift to another position.
- **time-step** (0 to 0.1, default 0.01): Larger values produce larger systematic opinion changes per tick. The effect from `sd-noise-opinion` does not change with the time step, so with a smaller `time-step` it matters relatively more.
- **bound** (0 to 5, default 10): "Bounded confidence": an agent only interacts with a neighbouring agent when both agent's opinions are within the range specified by `bound`. Low values mean agents only interact with others who already roughly agree with them. At 5 or 10 there is effectively no limit. (Note: The slider itself only goes up to 5, so once you move it you can only get back to 10 with setup-default or a preset.)

### Caution (Blume-Capel model only)

Each agent's total caution is worked out every tick as:

**caution = external-caution + personal starting caution + variance-effect-alpha × local variance**

- **min-init-caution** and **max-init-caution** (each 0 to 1, default 0): Each agent gets a caution value at the start, drawn at random between these two values.
- **external-caution** (0 to 1, default 0): Extra caution added to all agents. Automatically increased by **setup-habc1**.
- **variance-effect-alpha** (0 to 3, default 0): How strongly local variance contributes to caution. Higher values strengthen the tendency towards a neutral opinion where disagreement is greater. At 0, local disagreement does not affect caution. Automatically increased by **setup-habc2**.


## Presets


The presets do more than set sliders. They clear the world first, and the two habc presets also switch on an automatic rise in caution that no slider controls. So you cannot reproduce a preset by setting the sliders by hand.

Presets do not reset every setting to its default. Clearing the world leaves sliders and other interface controls unchanged. Any setting that neither the preset nor its setup code changes keeps its current value.

The two habc presets set the model, network and caution settings themselves. The other three do not, so check `model`, `topology` and the caution sliders before using them.

- **setup-default**: Sensible standard values.
- **setup-habc1**: Case 1 from the Blume-Capel paper. A grid of agents running the Blume-Capel model. Over the run, `external-caution` rises slowly by itself, from 0 to about 1 over 3,200 ticks, and then stops.
- **setup-habc2**: Case 2 from the Blume-Capel paper. A grid of agents running the Blume-Capel model, as in Case 1, but `external-caution` stays at 0. Instead, caution depends on how much each agent and its neighbours disagree. `variance-effect-alpha` gradually rises automatically, by 0.001 per tick until it reaches about 3 (after 3,000 ticks), so this local-disagreement effect gets stronger over time.
- **setup-black-pete**: The "Black Pete" scenario from the HIOM paper. All agents start with zero attention and slightly positive information (0.1).
**add-activists**: Use during a Black Pete run. Turn this on, then click on agents in the world. Each agent you click becomes an "activist": maximum attention and a clear negative opinion and information (-0.5). Turn the button off when you are done. On the grid you can click any square. On the other networks you have to click close to the centre of an agent; clicking the edge of a dot can miss.
- **setup-meat-eating-vegaterian**: The meat-eating vs. vegetarian scenario from the HIOM paper. Most agents lean slightly positive, while a random 20% start clearly negative with some attention. `bound` is set very low, so people only talk to those who already agree with them.
**perturbate-activists**: Use during a Meat-eating Vegeterian run. Takes half of all agents with a negative opinion and flips their opinion to the positive side, with maximum attention. Their information does not change. It is a sudden shock to the system.

The Black Pete and meat-eating presets were designed for the original HIOM `cusp` model.





## Display and plots

### The world (grid)

Each square or dot is one person.

**visualize** (opinion, attention, information or caution; default opinion) chooses what the colours show:

- **opinion**: Blue for positive, red for negative. The stronger the opinion, the darker the colour. Near-white means close to neutral.
- **information**: Same colour scheme as opinion, but showing information.
- **attention**: White for no attention, deeper red for more attention.
- **caution**: Deeper red for more cautious, black at 2 and above. The colour adds `external-caution` on top of each person's caution, so in the Blume-Capel model `external-caution` is counted twice. Towards the end of **setup-habc1** everyone therefore turns almost black.

### Plots and monitor

The **Opinion**, **Attention**, **Information**, **Caution** and **local variance opinion** histograms show the distribution of each variable across agents.

- **Opinion**: One peak in the middle means consensus or neutrality. Two peaks at the edges means polarisation.
- **means**: The average opinion (O), attention (A) and information (I) over time. Unlike the histograms, this plot averages over all agents. If everyone polarises into two equal camps, the average opinion can sit near 0 even though nobody is neutral. Check the Opinion histogram to tell the two cases apart.
- **local variance opinion**: High values mean people are surrounded by conflict. Only filled in with the MF Blume Capel model; in the other models every value is 0.
- **%active agents** (monitor): The share of people who were picked to start a conversation on the last tick. See `%active_agents` for how this relates to the target share.

## What happens each tick

Every tick does three things:

1. **Some people get talking.** Each agent starts a conversation with a probability equal to its attention, so attentive people talk more. A person who starts a conversation picks one random neighbour. If their opinions are close enough (see `bound`), both of them become a little more attentive and they swap some information.
2. **Things fade.** Everyone's attention fades a bit (unless too few people are active, see `%active_agents`), and information can fade and pick up random noise.
3. **Opinions move.** Each person's opinion moves a small step towards where its information and attention push it. The rule for that step depends on which `model` you picked.


