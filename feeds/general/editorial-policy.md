# Editorial Policy

Companion to the **scoring prompt**, which is provided alongside this document.
Read both before scoring.

- This document defines **what** I care about: topics, exclusions, penalties.
- The scoring prompt defines **how** to judge each article and what to return.

Section references of the form _prompt §4_ point at the other document.

This is a general news feed.
Its job is to carry the handful of stories a reader would regret missing entirely, plus what follows the topics below, and nothing else.

## 1. Topics

Primary subject only (prompt §4):

- **Artificial intelligence** — model releases and what they can newly do, regulation, deployment by states and public services, effects on work and on the information people see.
  Not funding rounds, executive moves or gadget reviews.
- **The United Nations and multilateral institutions** — the UN and its agencies, the Security Council, the WHO, the IMF and the World Bank, the COP summits: resolutions, treaties, peacekeeping, sanctions, famine and refugee declarations.
  Not a speech that commits nobody to anything.

Matching none of these does not disqualify an article.
A sufficiently significant story is worth passing on whether or not it touches anything on this list, which is what the score floor exists for (prompt §3).

## 2. Hard exclusions

A gate, not a penalty. Never included, including as a discretionary pick.

- **Commercial content** — anything selling, promoting, or sponsored; deals, affiliate-driven posts

**Precedence:** the exclusion wins over a topic match, and the test is the article's purpose, not its subject.
Reporting on an AI company is reporting; an article whose purpose is to move that company's product is commercial content, whoever published it.

## 3. Penalties

Heavy negative weight, not a gate.
Report which of these apply (prompt §5); they are weighed afterwards, and are not on their own a reason to drop an article.

- **Sport** — exempt for the deciding stages of a major international competition: a World Cup, an Olympics, a final
- **Day-to-day politics** — the procedural churn: polling, reshuffles, campaign noise, statements about statements.
  Exempt when a decision is actually taken and its effects outlast the political cycle
- **US-specific news** — a story whose consequences stop at that country's borders, and which therefore reaches a reader elsewhere as somebody else's local news: a state election, a local court case, a domestic personality.
  Exempt when the consequences travel

The exemptions are the point of these bullets.
Two of the three penalise a whole field, so a story from that field that genuinely matters must come through unpenalised rather than clawing its way back.
