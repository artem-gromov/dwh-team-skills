---
name: switch-chatgpt-credential
description: Use when switching or identifying a ChatGPT OAuth account.
version: 1.0.0
author: Hermes Agent
license: MIT
metadata:
  hermes:
    tags: [hermes, openai-codex, chatgpt, oauth, credential-pool]
    related_skills: [hermes-agent]
---

# Switch and Identify ChatGPT Credentials

## Overview

Use this procedure when the user wants Hermes to send future `openai-codex` requests through a specific ChatGPT OAuth credential, or asks which ChatGPT account is selected.

Named credentials currently used in this profile:

- `corporate-chatgpt` — corporate account
- `home-chatgpt` — personal/home account

Hermes currently has no supported **per-session** credential selector. Switching changes the **global pool priority** used for future credential resolution; it cannot change an API request that is already in flight. State this scope when reporting the result.

## When to Use

- User says to switch Hermes/ChatGPT/Codex to `corporate-chatgpt` or `home-chatgpt`.
- User asks which ChatGPT/OpenAI account the current session is using.
- User wants corporate used first and home used only after corporate reaches a quota, rate-limit, or auth failure.

Do not use for model/provider selection; use the regular Hermes model workflow instead.

## Desired Default Policy

Keep `fill_first` configured for `openai-codex`:

```bash
hermes config set credential_pool_strategies.openai-codex fill_first
```

With `corporate-chatgpt` at priority 0 and `home-chatgpt` at priority 1, Hermes uses corporate first, then rotates to home if corporate is unavailable. Do **not** use `round_robin` for this policy: it spends both credentials alternately while corporate is still healthy.

## Safe Switching Procedure

1. **Inspect before editing.**

   ```bash
   hermes auth list openai-codex
   ```

   Confirm the exact target label is present. Never treat the displayed numeric index as a stable identity: priority order can change after selection.

2. **Set `fill_first`.**

   ```bash
   hermes config set credential_pool_strategies.openai-codex fill_first
   ```

3. **Move the requested label to priority 0.** Replace `TARGET_LABEL` with `corporate-chatgpt` or `home-chatgpt`. The command prints no token material.

   ```bash
   python3 -c 'from dataclasses import replace; from agent.credential_pool import load_pool; target="TARGET_LABEL"; p=load_pool("openai-codex"); entries=p.entries(); matches=[e for e in entries if e.label==target]; assert len(matches)==1, f"Expected exactly one {target!r}, got {len(matches)}"; ordered=matches+[e for e in entries if e.id!=matches[0].id]; p._entries=[replace(e, priority=i) for i,e in enumerate(ordered)]; p._persist(); selected=p.select(); print(f"selected={selected.label} id={selected.id}")'
   ```

   Completion criterion: it prints `selected=TARGET_LABEL`.

4. **Verify through the public CLI.**

   ```bash
   hermes auth list openai-codex
   hermes config get credential_pool_strategies
   ```

   Completion criterion: target has `←`, appears first, and configuration says `openai-codex: fill_first`.

5. **Report scope.** Say which credential is now selected for future requests, name the fallback, and state that this is global pool priority rather than isolated session pinning.

## Identify the Selected ChatGPT Account

1. **Read the selected label and stable ID without secrets.**

   ```bash
   python3 -c 'from agent.credential_pool import load_pool; p=load_pool("openai-codex"); e=p.entries()[0]; print("selected_label="+e.label); print("selected_id="+e.id)'
   ```

2. **Read only safe OAuth identity claims.** This decodes the access-token payload locally but never prints the token itself or any refresh token.

   ```bash
   python3 -c 'import base64,json; from agent.credential_pool import load_pool; e=load_pool("openai-codex").entries()[0]; claims={}; parts=e.access_token.split(".");
if len(parts)==3: claims=json.loads(base64.urlsafe_b64decode(parts[1]+"="*(-len(parts[1])%4)))
for k in ("email","preferred_username","upn","name","sub","account_id","chatgpt_account_id","organization_id","org_id"):
 if claims.get(k) is not None: print(k+"="+str(claims[k]))'
   ```

3. **Report only what exists.** Prefer e-mail/name if present; otherwise give the label and OAuth `sub` identifier. Do not guess an e-mail from the label or claim that an OAuth subject proves a particular person without corroboration.

4. **Cross-check public state.**

   ```bash
   hermes auth list openai-codex
   ```

   Completion criterion: CLI `←` agrees with the label inspected above.

## Removing a Credential

First obtain stable IDs without token material:

```bash
python3 -c 'from agent.credential_pool import load_pool; p=load_pool("openai-codex"); [print(f"priority={e.priority} id={e.id} label={e.label!r}") for e in p.entries()]'
```

Remove only the user-confirmed stable ID:

```bash
hermes auth remove openai-codex STABLE_ID
```

Verify afterward with `hermes auth list openai-codex`. Do not use a numeric index after any pool selection.

## Common Pitfalls

1. **Using a displayed list number as stable identity.** It is priority order. Use the immutable ID for deletion.
2. **Using `round_robin` for primary-plus-fallback intent.** It alternates healthy accounts. Use `fill_first`.
3. **Claiming a true per-session switch.** Current Hermes pool selection is global; no supported per-session credential pin exists.
4. **Displaying tokens.** Inspect only labels, stable IDs, priorities, and explicitly requested safe identity claims.
5. **Equating display label with account identity.** Labels can be renamed; use OAuth claims when the user asks who authenticated.

## Verification Checklist

- [ ] Target label exists exactly once before switching.
- [ ] `openai-codex` uses `fill_first`.
- [ ] Target is first and marked `←` in `hermes auth list openai-codex`.
- [ ] Fallback credential remains in the pool.
- [ ] Account identity report exposed no tokens or refresh tokens.
- [ ] User was told the global-vs-per-session limitation.
