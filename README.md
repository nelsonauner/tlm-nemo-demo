# Using Trustworthy Language Model (TLM) with NVIDIA NeMo Guardrails


As of Sep 12, 2024, the TLM functionality has not been publically released, so we'll install from development:


```
(virtualenv)$git clone https://github.com/NVIDIA/NeMo-Guardrails
(virtualenv)$pip install -e NeMo-Guardrails
```

## Required environmental variables

Ensure you have `CLEANLAB_API_KEY` defined. A simple way to do this for testing is to use `export`: 

`(virtualenv)$export CLEANLAB_API_KEY=<Get your API key at https://app.cleanlab.ai/tlm>`


Once enabled, update your `config.yml` to use cleanlab trustworthiness as an output rail:

```
# config code

output:
  flows:
    - cleanlab trustworthiness 
```

You can also customize the response by overriding the default config by creating a new colang file (e.g. `tlm.co`: 

```
define subflow cleanlab trustworthiness
  $result = execute call cleanlab api

  if $result.trustworthiness_score < 0.8
    if $config.enable_rails_exceptions
      create event CleanlabTrustworthinessRailException(message="Trustworthiness score is below threshold")
    else
      bot response untrustworthy
    stop

define bot response untrustworthy
  "I'm sorry, please let me escalate this question to a manager"
  # Additional actions
```

Either way, run the guardrails system as normal: `nemoguardrails chat --config=.`, or via  your preferred interface (docker, Python API, etc) 
