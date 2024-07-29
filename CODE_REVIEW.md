## Review

- [My fork](https://github.com/awebb-nz/procrastination_cogsci2024)

### General

- Overall, nice clean code. Well done!

- Mostly really good commenting
    - There are a few deviations in style. Do you auto generate from a template?
    - Have you tried to use type annotations on function arguments, rather than putting the types in the comments?

- Some of my comments predate Surabhi suggestions (and the resulting changes). Feel free to disregard those

- Each of your model files starts by defining slightly different environment and reward structures. It might be worth having a more detailed comment for each of them, to explain what the difference is 

### `README.md`

- Fixed spelling mistakes

### `data_clusters.py`

- You are printing out a lot of stuff, is it all necessary? Can you explain it?
- You should be printing what images you are saving, and where
- You are generating parts of a figure, rather than an actual figure. Is that worth clarifying in the README?
    - Also, the parts are not ordered as in the paper. Is that worth noting?
- You should always be a bit wary if you find yourself looping through a dataframe 
    - Instead, prefer broadcasting. *E.g.*, rewriting the loop in the following way
```python
# Instead of the loop
cumulative_normalised = []
for i in range(len(data_relevant)):
    temp = np.array(
        ast.literal_eval(data_relevant['cumulative progress'][i]))
    cumulative_normalised.append(temp/data_relevant['Total credits'][i])
data_relevant['cumulative progress normalised'] = cumulative_normalised

# Use an applied function on the row
def cumulative_norm(row):
    temp = np.array(ast.literal_eval(row['cumulative progress']))
    return temp/row['Total credits']

data_relevant['cumulative progress normalised'] = \
    data_relevant.apply(lambda row: cumulative_norm(row), axis=1)
```

- If you do have to loop over values in a column, make that more explicit:
```python
for vs in data_relevant['cumulative progress'].values:
    temp = np.array(ast.literal_eval(vs))
```

- Line 94: Why are you loading the data? Where are you saving it?

### `task_structure.py`

- General:
    - You document the type of each variable you pass in. You could use types for that instead...

- `reward_threshold`:
    - Line 68: `unitl` => `until`
    - Lines 35 -81: This looks like two separate loops to me; 0 - thr and thr - number of states

- `binomial_pmf`:
    - Is there are reason you defined your own, rather than using `scipy.stats.binom`?
    - Line 256: `binomail` => `binomial`

- `generate_interest_rewards`:
    - You don't document the returned values here

- `deterministic_policy`/`softmax_policy`:
    - Here, for some reason, you change the way you comment your functions...

### `mpd_algms.py`

- General:
    - In a few functions you give the type of *e.g.*, `T` to be a list, but it is a list of lists, right? Is there a way to make the clearer?
    - Also, I am a bit confused about how you chose which variables to capitalise, and which not...

- `find_optimal_policy`/`find_optimal_policy_prob_rewards`/`find_optimal_policy_T_time_dep`:
    - These functions are all super similar 
        - They differ only in the "Bellman equation"-part, right?
    - I would suggest either:
        1. Having a single base function where you supply the q-value function as an argument; or
        2. At least being really clear in the comments about what the difference is

- `forward_runs`:
    - Lines 303, 304: Why are you filling the arrays with `100`?
        - You are using pretty small horizons. It may be just as fast to build a python array, and build an ndarray afterwards
    - Also, is this function being used anywhere?

- `forward_runs_prob`:
    - Lines 349, 350: Why are you filling the arrays with `100`?

- `forward_runs_T_time_dep`:
    - Lines 395, 396: Why are you filling the arrays with `100`?
    - Also, is this function being used anywhere?


### `basic_model.py`

- Where does `basic_no_discounts.svg` come from?
    - Actually, I think you may have added that code, now...
    - But, now you have 2 copies of the same code that differ only on the value of `discount_factor` and the name of the saved file
    - You should be able to extract this all to a function that takes those two params, or run it in a loop...
    - I guess the same is true for all of the plots that you are saving here. It would definitely be worth seeing if you could extract to a function, and just pass in the params that differ between them
- I was slightly confused by the fact that `reward_func`, `effort_func`, *etc.*, were not functions (in the computational sense)...
- You are using `1000` to set the number of trajectories. Is this number important? Can you make it a named variable/constant?

### `immediate_reward_model.py`

- Line 61: What is this `T` for? I think it is overwritten pretty immediately...
- Line 115: I don't think `EXPONENT` is ever used...
- Here again, there is quite a bit of repeated code between the different plot generations. Can some of it be pulled out into a function?
    - *E.g.*, lines 127 - 163 and 173 - 207

### `defection_model.py`

- Line 83: You define `discount_factor_cost` here, then define some unrelated stuff, then use `discount_factor_cost` on line 90
    - Can you define this just before you use it? (*i.e.*, define it after the `colors` and `plt` stuff)
- Line 85: As far as I can see `initial_state` is a constant. `INITIAL_STATE`?
- Line 127: Is `distance_defect` used anywhere?

### `no_commitment.py`

- On my linux and windows machines, I often get the colours reversed compared to figure 5B...
- Line 44: As far as I can see `initial_state` is a constant. `INITIAL_STATE`?
- Lines 64 and 89:
    - Why are you looping 3 times? As in, what is loop for?
    - You don't actually use `i` anywhere. Are you aware that you can write `for _ in range(3):` if you don't care about the loop variable?
- Lines 76+ and 81+ are exactly the same... I assume you can get rid of one of them
- Again, lines 56 - 70 and 81 - 95 seem pretty similar. Can you extract a function?
