# Ollama Cloud: How It Works, What It Really Costs per Token, and When Renting a GPU Server Makes More Sense

"Ollama cloud" means two different things in practice, and picking the wrong one costs you money. The first is Ollama's official cloud service: you run hosted open models through the Ollama app, CLI, or API without downloading weights or owning a GPU. The second is running Ollama yourself on rented cloud hardware — same commands, same API, but on a server you control and pay for by the hour.

The short answer: for most people who want to use large open models occasionally or daily, Ollama's official cloud is the cheaper and easier path — the Free tier gets you started, and Pro at $20/month includes $60 of usage credits. A rented GPU server starts to make sense when you want flat costs, need custom or fine-tuned weights, or want an inference endpoint that no one else meters or manages.

> **Quick picks**
> - Just experimenting with big models, no GPU at home → Ollama Cloud's Free tier, then Pro if you're using it most days
> - Want per-token prices gone, custom models, or a private always-on endpoint → rent a GPU server and run standard Ollama on it. 👉 [Check Vultr's current pricing](https://www.vultr.com/pricing/?ref=7268283)

## What Ollama Cloud actually is

Ollama Cloud is Ollama's hosted inference service. Models run on Ollama's infrastructure, so nothing downloads to your machine and no local GPU is required — the same chat interface you'd use locally just silently offloads to their cloud. Cloud models show up alongside local ones, marked with a `-cloud` suffix (for example `gpt-oss:120b-cloud`).

Two ways in. The simple one: install the Ollama app or CLI, run `ollama signin`, and cloud models work like local ones. The developer route: create an API key, set it as an environment variable, and hit Ollama's chat API directly — no Ollama installation needed at all. That direct API also speaks the OpenAI and Anthropic client formats, each supporting a subset of the original API, so existing code often needs only a base-URL change.

One naming detail that trips people up: through the direct API you use the exact model name from the tags list (like `gemma4:31b`), while in the app or CLI the same model is `gemma4:cloud`. Different name, same model.

## Ollama Cloud pricing: the four plans

There are four plans: Free at $0, Pro at $20/month, Team at $500/month, and Enterprise at custom pricing. All of them are credit-based — you get a dollar amount of usage each month and pay per token beyond the model rates.

| Plan | Price | What's included | Billing | Best for |
| --- | --- | --- | --- | --- |
| Free | $0 | Starter usage credits, starter models; buy credits to unlock all models | Pay-as-you-go | Trying it out |
| Pro | $20/mo or $200/yr | $60 of usage credits/month, larger pro models, multiple models running concurrently, fast mode (coming soon) | Monthly or annual | Daily individual use |
| Team | $500/mo (early access) | Everything in Pro, unlimited users, $1,000 of credits/month shared across the team, central billing, priority support | Monthly | Teams pooling usage |
| Enterprise | Custom | Model access controls, cost budgets per user and API key, private Slack support, security questionnaires | Custom | Larger organizations |

### What you actually pay per token

Rates vary wildly between models — roughly 50x between the cheapest and most expensive output tokens. Here's the spread on some of the current catalog (per million tokens):

| Model | Input | Cached input | Output |
| --- | --- | --- | --- |
| gpt-oss:20b | $0.07 | $0.035 | $0.30 |
| gpt-oss:120b | $0.15 | $0.014 | $0.60 |
| gemma4 | $0.14 | $0.05 | $0.40 |
| deepseek-v4.1-flash | $0.30 | $0.006 | $1.20 |
| deepseek-v4-pro | $1.32 | $0.044 | $3.96 |
| minimax-m3 | $0.60 | $0.12 | $2.40 |
| glm-5.3 | $1.40 | $0.26 | $4.40 |
| kimi-k3 | $3.00 | $0.30 | $15.00 |

There's also an off-peak discount: outside 12:00–18:00 UTC on weekdays and all day on weekends, listed models drop to roughly half — deepseek-v4.1-flash output falls from $1.20 to $0.60 per million tokens. If your workload can run overnight or on weekends, that's a real saving.

The credits math matters more than the sticker price. Pro's $60 of monthly credits buys 100 million output tokens on gpt-oss:120b — but only 4 million on kimi-k3. Same credits, very different mileage. On the $200/year annual option, you pay about $16.67 a month while keeping the full $60 in credits, which is the best value on the plan sheet if you'll use it consistently.

### How the monthly credits behave

Credits reset monthly on the day your subscription started, including on annual plans; the Free plan resets from your signup date. Unused credits do not roll over. When you exceed the included amount, Ollama spends from any credits you've purchased, and Team usage draws from one shared balance.

## The limitations worth knowing before you build on Ollama Cloud

Three practical constraints affect anyone planning to stick with the official service: model retirement, a curated catalog, and costs that scale with usage.

**Models get retired.** Ollama retires older cloud models as newer open-source releases land, and 2026 already saw a coordinated round: gemma3:12b, 27b, and 4b were all retired on July 15, 2026 with gemma4:31b as the replacement, and deepseek-v3.1 and deepseek-v3.2 went the same day. Upcoming retirements show in your usage settings, and Ollama notifies affected users in advance — but any app built on a retired model needs updating. Local models are unaffected; downloaded weights keep working forever.

**The catalog is curated.** You can only run what Ollama hosts. Fine-tuned weights, niche GGUF builds, or a model variant they haven't added are simply not available. That's the single biggest functional gap versus self-hosting.

**The meter never stops.** There's no flat unlimited tier. Heavy agent workloads with long prompts and big outputs burn credits proportionally, and cached-input discounts only soften the blow so much.

On privacy, the official position is clear: prompts and responses are not used to train models, and per the FAQ, never logged. Infrastructure is hosted primarily in the United States, with possible routing through Europe and Singapore for capacity, and Ollama says its NVIDIA Cloud Provider partners operate under no-logging and zero-retention requirements. Your data still passes through a third party's shared infrastructure — for most workloads that's fine, for some it isn't.

## The other "Ollama cloud": renting a GPU server and running it yourself

The alternative is to rent a virtual machine with an NVIDIA GPU, install Ollama on it, and treat that box as your own inference server. The CLI and API are identical to your laptop's — `ollama pull`, `ollama run`, and everything that talks to port 11434 keeps working; you just point it at your server's IP instead of localhost. Billing is by the hour rather than per token.

Vultr fits this use case well: it's a cloud infrastructure provider offering standard VMs, NVIDIA GPU instances, and bare metal across data centers on several continents. Cloud instances bill hourly and you can destroy them whenever you like, so a weekend batch job costs you a weekend, not a month. There are no fixed plans — you size an instance configuration and pay for what you run.

| Vultr product | What you'd run on it | Billing | Good fit | Link |
| --- | --- | --- | --- | --- |
| Cloud GPU | NVIDIA data-center GPUs; large models and API serving | Hourly; destroy anytime to stop billing | 20B+ models, always-on endpoints | [ View Cloud GPU configurations and current pricing](https://www.vultr.com/products/cloud-gpu/?ref=7268283) |
| Cloud Compute | General-purpose CPU VMs | Hourly | Small quantized models at modest speed, dev and testing | [ View cloud server configurations](https://www.vultr.com/products/cloud-compute/?ref=7268283) |
| Bare Metal | Dedicated single-tenant physical servers | Flat monthly rate | Constant heavy workloads with no shared infrastructure | [ View bare metal configurations](https://www.vultr.com/products/bare-metal/?ref=7268283) |

The setup takes about ten minutes:

1. Create an account — new sign-ups through a referral link usually come with a trial credit; the current amount shows at registration.
2. Deploy a Cloud GPU instance in the data center closest to your users.
3. SSH in and install Ollama with the one-line install script from Ollama's site.
4. Pull a model that fits your VRAM, then run it.
5. Point your existing tools and scripts at your server's IP on port 11434.

If your code already calls Ollama locally, step 5 is often the only change. 👉 [Create an account on Vultr](https://bit.ly/VulTR) and you can have a model serving before your coffee cools.

## Cost head-to-head: credits versus an hourly GPU

For light and moderate use, the official cloud wins on price by a wide margin. A rented GPU at representative market rates puts things in perspective (third-party comparison data from early 2026):

| Provider example | GPU | VRAM | Approx. hourly rate |
| --- | --- | --- | --- |
| AWS g5.xlarge | A10G | 24 GB | ~$1.00 |
| GCP (T4 attached) | T4 | 16 GB | ~$0.50 |
| Lambda Labs | A10 | 24 GB | ~$0.60 |
| RunPod | RTX 4090 | 24 GB | ~$0.69 |
| Azure NC6s v3 | V100 | 16 GB | ~$3.00 |

Run a $0.60/hour instance 24/7 and you're at roughly $432 a month. Pro costs $20 with $60 of credits included — enough for around 100 million output tokens of gpt-oss:120b, which is more than nearly any individual will generate. The hourly server only beats that when your token volume is genuinely industrial, or when the value isn't in tokens at all: custom weights, an isolated environment, or metering you never have to think about.

The same comparison notes that a local RTX 4090 (around $1,600) pays for itself in under four months of continuous 24/7 use versus a $0.60/hour rental. Which tells you what renting is actually for: flexibility, not beating owned hardware at full duty cycle. Spin up for a project, destroy when done, pay for the hours — that's the model.

## Which one fits your situation

Match your usage to the setup:

- Occasional experiments and chat → Ollama Cloud Free, add credits when you hit the starter limit
- Daily personal use → Pro at $20/month, leaning on off-peak windows and cheaper models like gpt-oss:120b to stretch the $60 of credits
- Building an app on cloud models → Pro, but design for model retirements from day one; check the usage settings page before you ship
- A team sharing usage and billing → Team at $500/month with $1,000 of pooled credits
- Custom or fine-tuned weights, flat predictable costs, an always-on private API, or high steady volume → rent a GPU server and run Ollama yourself. 👉 [Check current Cloud GPU pricing on Vultr](https://www.vultr.com/products/cloud-gpu/?ref=7268283)

## FAQ

**Do I need to download models to use Ollama Cloud?**
No. Cloud models run remotely and never download to your machine — in the app and CLI they're the entries with the `-cloud` suffix.

**Can I keep using my OpenAI or Anthropic SDK code?**
Yes. Ollama's direct API supports both client formats, each covering a subset of the original API, so most integrations need only an endpoint and key change.

**Does Ollama train on my prompts?**
Per Ollama's stated policy, no — prompts and responses are not used to train models, and the FAQ states they are never logged.

**What happens when a cloud model I rely on gets retired?**
Ollama announces deprecations in advance by email and on its website, and upcoming retirements for models you've recently used appear in your usage settings. Switch before the retirement date; locally downloaded models are unaffected.

**Do the cloud models support tool calling?**
Yes. Models trained for tool use are tested for tool calling and real agent workflows before they go live.

One last check before you commit either way: if your usage is bursty and small, start on the free credits and see what a typical month actually costs in tokens — you may be surprised how little it is. If you already know you want the box, the fastest path is to pick a GPU configuration, deploy, and destroy it whenever it's idle. 👉 [See Vultr's current instance pricing](https://www.vultr.com/pricing/?ref=7268283)
