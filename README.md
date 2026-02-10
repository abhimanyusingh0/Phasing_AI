# Phising_AI

Architecture (No HuggingFace, No External Models)
1️⃣ Mailbox Listener (Azure Function)
Connect via Microsoft Graph
Poll reported-phishing mailbox every 5 minutes
Extract:
Subject
Body
Sender
URLs
Headers (SPF/DKIM)
This part is simple.
2️⃣ Technical Risk Scoring (No AI yet)
Compute:
External sender? (+ score)
Domain similarity to bank? (+ score)
SPF/DKIM fail? (+ score)
Domain age < 30 days? (+ score)
Suspicious keywords? (+ score)
Multiple shortened URLs? (+ score)
This alone will auto-close many.
You’d be surprised how effective good scoring logic is.
3️⃣ Linguistic AI Layer (Enterprise Safe)
Instead of HuggingFace, use:
👉 Azure OpenAI (if allowed internally)
This stays inside Microsoft tenant.
You send:
Copy code

Classify this email as:
- phishing
- safe
- uncertain

Return probability (0–1) and short reasoning.
This avoids downloading models.
4️⃣ Confidence Thresholding
Example:
AI > 0.85 AND technical score high → Auto-confirm phishing
AI < 0.25 AND technical score low → Auto-close
Otherwise → Analyst
Very clean logic.
🧠 Why This Works Even Without HF Models
Because:
Defender already catches obvious phishing.
Your remaining workload is mostly borderline cases.
Azure OpenAI handles semantic understanding.
Your rule engine handles infrastructure anomalies.
You don’t need to train anything.
⚠ Important: Make It Deterministic
To keep auditors happy:
Use temperature = 0
Ask model to output strict JSON
Log prompt + response
Store decision trace
This keeps it reproducible.
🏗 Final Clean Architecture
Copy code

Reported Mailbox
       ↓
Azure Function
       ↓
Feature Extraction
       ↓
Technical Scoring Engine
       ↓
Azure OpenAI Classification
       ↓
Final Risk Score
       ↓
Auto-close / Auto-confirm / Analyst queue
That’s it.
No model downloads. No custom ML training. No heavy infra.
💰 Cost Reality
If you have:
500 reports/day
Each email ~3–5k tokens
Monthly cost is manageable compared to analyst salary hours.
And you can:
Only send borderline cases to OpenAI
Further reduce cost
