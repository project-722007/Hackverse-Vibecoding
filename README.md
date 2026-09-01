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



Agent Architecture:

The TF3 Financial Intelligence Dashboard is a Streamlit-based web application designed to display autonomous financial intelligence. It serves as an executive interface for real-time multi-agent signal synthesis and portfolio analytics. The application reads synthesized financial data and visualizes actionable insights, execution metrics, and underlying agent reasoning.


1.The application features a sidebar navigation menu with access to the "Dashboard", "Agent Traces", "Risk & Portfolio", and "System Logs".

  i. Dashboard: Acts as the primary viewport containing executive summaries, action headers, interactive charts, and the AI assistant.


  ii. Agent Traces: Acts as a diagnostic view to inspect the raw JSON outputs of the specialized sub-agents.


  iii. Risk & Portfolio: Displays the active behavioral constraints alongside the active user ID and simulated forward performance metrics.


  iv. System Logs: Renders the complete, raw JSON payload for deep system inspection.


2.Users can customize a "User Behavioral Profile" by adjusting their Risk Tolerance, Mandate, and Max Drawdown Limit.


3.An execution button allows users to trigger the end-to-end pipeline, which runs tf1_engine.py and tf2_engine.py sequentially using system commands.


4.The main dashboard displays Key Performance Indicators (KPIs), including the Target Asset, Synthesized Signal, Pipeline Latency, and 30D Forward Accuracy.


5.The application generates interactive visualizations using Plotly, specifically a 30-day simulated asset performance line chart and a portfolio risk concentration gauge chart.


6.The interface includes a dedicated section for AI synthesis reasoning, source citations, and warnings for degraded data.


7.An integrated AI Assistant chat interface allows users to ask questions about portfolio balance and recommended actions.
 Application Structure.



Decision & Safety Logic:

1.The application requires several standard and third-party Python libraries: streamlit, json, os, pandas, numpy, and plotly (specifically plotly.graph_objects and plotly.express).


2.To populate the dashboard with data, the application attempts to read from a local file named tf2_output.json.


3.If tf2_output.json is not found, the application falls back to a default state and prompts the user to execute the pipeline.


4.The pipeline execution relies on specific local Python 3.13 paths (/Library/Frameworks/Python.framework/Versions/3.13/bin/python3) to trigger the underlying engine scripts.
