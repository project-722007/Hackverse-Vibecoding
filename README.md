#**Agent Architecture:**

The agent works as an intelligent data pipeline (TF1 RETRIEVAL) that collects both numbers and news, cleans them up, and packages them neatly for trading decision software (TF2). It gathers information using two straightforward pathways:  

1.**Market Numbers Engine**: Pulls real-time stock prices and automatically calculates key trading indicators like price momentum, trading volume, and technical trend lines.

2.**Document Reader Layer**: Searches through stored company filings and reports using an AI database (Chroma) to find text relevant to the company.

3.**Data Packager**: Combines the live market numbers and the document findings into a single, organized JSON file (v2.0 format) that downstream systems can easily read. 


#**Decision & Safety Logic:**

1.**Backup Plan for Live Prices**: If live stock price data is missing or connection fails, the agent catches the error, sets the market status to "degraded," and fills in safe default values so the whole app doesn't crash. 

2.**Backup Plan for Documents**: If company filings are missing or the database is offline, the agent automatically switches to a backup mode (fallback active: true) and assigns a cautious trust score (0.5) to default safety disclosures.

3.**Clear Safety Warnings**: Every output includes clear status labels (market feed degraded, fallback active). This lets the judges or decision systems immediately know whether the decision was made on live, complete data or backup safety data.

4.**Graceful Degradation**: Both quantitative and RAG layers feature try/except fallbacks. If live market APIs fail or return empty feeds, the module flags feed status: "degraded" and returns empty technical placeholders rather than breaking execution. 

5.**RAG Fallback Risk Handler**: If a local vector store or expected PDF filing (e.g., Filing RELIANCE.NS.pdf) is missing, the system automatically engages a system archive baseline (fallback active: true). It returns default disclosures with a baseline confidence score, preserving data pipeline uptime.

6.**Dynamic Signal Schema**: Outputs a contract (v2.0) that explicitly reports system status flags (market feed degraded, fallback active, data availability) so downstream evaluation models know whether to rely on full signal strength or apply precautionary risk adjustments.
