
Good question. This is exactly where seniors will probe next.

I’ll give you integration tests, not unit tests — meaning controller → service → validator → domain → response flow.
I’ll also map each test directly to Jira intent, so you can justify why each one exists.

No fluff.


---

What “integration test” means here (context)

An integration test should verify:

API / service entry point

Full validation pipeline execution

Real ValidationStrategy selection

Real ValidationFailure output

No mocking of validators themselves


You are testing behavior, not methods.


---

1️⃣ Happy-path integration tests (must-have)

✅ IT-01: Controlled vessel with all mandatory insurance data

Given

Vessel status = CONTROLLED

Live, financed, not snapshot

Insurance types:

Hull & Machinery → contract exists → insurer exists

War Risk → contract exists → insurer exists

P&I → contract exists



When

Mark asset as CONTROLLED (or validation endpoint hit)


Then

Validation passes

validationFailures = empty

Asset transitions to CONTROLLED successfully


👉 Why needed

Proves no false positives

Baseline sanity test



---

2️⃣ Contract-missing integration tests (core Jira scenarios)

❌ IT-02: No insurances at all

Given

input.getInsurances() = null or empty


Then

Failures:

NO_HULL_AND_MACHINERY_CONTRACT

NO_WAR_RISK_CONTRACT

NO_P_AND_I_CONTRACT



👉 Covers Jira row 1


---

❌ IT-03: Hull & Machinery missing contract

Given

War Risk → valid

P&I → valid

Hull & Machinery → no contract


Then

Failure:

NO_HULL_AND_MACHINERY_CONTRACT



👉 Ensures per-insurance isolation


---

❌ IT-04: War Risk missing contract

Given

Hull & Machinery → valid

P&I → valid

War Risk → no contract


Then

Failure:

NO_WAR_RISK_CONTRACT




---

❌ IT-05: P&I missing contract

Given

Hull & Machinery → valid

War Risk → valid

P&I → no contract


Then

Failure:

NO_P_AND_I_CONTRACT




---

3️⃣ Insurer-missing integration tests (most important logic)

❌ IT-06: Hull & Machinery contract exists but insurer missing

Given

Hull & Machinery → contract exists → insurers empty

War Risk → valid

P&I → valid


Then

Failure:

HULL_AND_MACHINERY_INSURER_MISSING


❌ No contract failure


👉 Proves isInsurerMissing() works only when contract exists


---

❌ IT-07: War Risk contract exists but insurer missing

Given

War Risk → contract exists → insurers empty

Hull & Machinery → valid

P&I → valid


Then

Failure:

WAR_RISK_INSURER_MISSING




---

✅ IT-08: P&I contract exists, insurer missing (should PASS insurer validation)

Given

P&I → contract exists → insurers empty


Then

❌ No insurer-related failure

✔ Only contract validation applies


👉 This test is critical to prove Jira alignment
(“Insurer = N/A for P&I”)


---

4️⃣ Mixed failure integration tests (real-world scenarios)

❌ IT-09: Multiple failures combined

Given

Hull & Machinery → contract exists, insurer missing

War Risk → no contract

P&I → no contract


Then Failures:

HULL_AND_MACHINERY_INSURER_MISSING

NO_WAR_RISK_CONTRACT

NO_P_AND_I_CONTRACT


👉 Proves:

Validator accumulates failures

No short-circuiting



---

5️⃣ Validation gating tests (canValidate)

🚫 IT-10: Asset NOT controlled → validation skipped

Given

Asset status ≠ CONTROLLED


Then

Validator not triggered

No insurance-related failures


👉 Prevents false blocking in other workflows


---

🚫 IT-11: Snapshot vessel → validation skipped

Given

input.isSnapshot() == true


Then

No validation failures



---

🚫 IT-12: Excluded category (e.g., CONTAINER_BOXES)

Given

Vessel category in CATEGORIES_TO_EXCLUDE


Then

Validation skipped



---

6️⃣ Defensive integration tests (production safety)

⚠️ IT-13: Insurance exists but contracts = null

Given

Insurance(type=HULL_AND_MACHINERY, contracts=null)

Then

Treated as “no contract”

NO_HULL_AND_MACHINERY_CONTRACT

No NPE


👉 Proves robustness of stream guards


---

⚠️ IT-14: Contract exists but insurers = null

Given

Contract(insurers=null)

Then

*_INSURER_MISSING

No NPE



---

Minimal recommended integration test set (if time is tight)

If your senior asks “what’s the minimum?”, answer this:

> 8 integration tests



1. Happy path


2. No insurance at all


3. H&M contract missing


4. War Risk contract missing


5. P&I contract missing


6. H&M insurer missing


7. War Risk insurer missing


8. Mixed failures



That covers 100% Jira behavior.


---

What this tells seniors about you

You think in business scenarios

You validate end-to-end behavior

You understand why each rule exists

You avoid over-testing internals


If you want next, I can:

Convert these into JUnit test method names

Or write one full Spring Boot integration test example


Just say the word.
