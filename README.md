**CAUTION: RAN OUT OF TOKENS FOR GEMINI API KEY**


# **Agent Architecture:(TF1)**

The agent works as an intelligent data pipeline (TF1 RETRIEVAL) that collects both numbers and news, cleans them up, and packages them neatly for trading decision software (TF2). It gathers information using two straightforward pathways:  

1.**Market Numbers Engine**: Pulls real-time stock prices and automatically calculates key trading indicators like price momentum, trading volume, and technical trend lines.

2.**Document Reader Layer**: Searches through stored company filings and reports using an AI database (Chroma) to find text relevant to the company.

3.**Data Packager**: Combines the live market numbers and the document findings into a single, organized JSON file (v2.0 format) that downstream systems can easily read. 


# **Decision & Safety Logic:(TF1)**

1.**Backup Plan for Live Prices**: If live stock price data is missing or connection fails, the agent catches the error, sets the market status to "degraded," and fills in safe default values so the whole app doesn't crash. 

2.**Backup Plan for Documents**: If company filings are missing or the database is offline, the agent automatically switches to a backup mode (fallback active: true) and assigns a cautious trust score (0.5) to default safety disclosures.

3.**Clear Safety Warnings**: Every output includes clear status labels (market feed degraded, fallback active). This lets the judges or decision systems immediately know whether the decision was made on live, complete data or backup safety data.

4.**Graceful Degradation**: Both quantitative and RAG layers feature try/except fallbacks. If live market APIs fail or return empty feeds, the module flags feed status: "degraded" and returns empty technical placeholders rather than breaking execution. 

5.**RAG Fallback Risk Handler**: If a local vector store or expected PDF filing (e.g., Filing RELIANCE.NS.pdf) is missing, the system automatically engages a system archive baseline (fallback active: true). It returns default disclosures with a baseline confidence score, preserving data pipeline uptime.

6.**Dynamic Signal Schema**: Outputs a contract (v2.0) that explicitly reports system status flags (market feed degraded, fallback active, data availability) so downstream evaluation models know whether to rely on full signal strength or apply precautionary risk adjustments.

# **System Architecture & Tech Stack:(TF2)**

1.The engine is built using Python, leveraging asyncio for asynchronous concurrency and Pydantic for strict data validation schemas.  

2.It integrates LangChain components including ChatGoogleGenerativeAI and ChatPromptTemplate to manage model communication. 

3.Execution requires a valid GOOGLE_API_KEY environment variable to authenticate with Google's generative models.  

4.Omitting a custom user profile causes the system to automatically fall back to a low risk tolerance profile featuring a capital preservation mandate and a 10% maximum drawdown limit. 


# **Structured Output Schemas:(TF2)**

1.TechnicalAgentOutput defines fields for 14-period RSI, MACD, 5-day momentum, volume ratio, categorical trading signals (BUY, SELL, NEUTRAL), and a confidence score between 0.0 and 1.0.

2.FundamentalAgentOutput captures qualitative summaries, identified risk flags, and source document citations extracted from RAG contexts. 

3.SentimentAgentOutput tracks overall sentiment categories (BULLISH, BEARISH, NEUTRAL), headline summaries, and numerical sentiment scores ranging from -1.0 to 1.0. 

4.RiskAlignment structures the profile type and allocated maximum drawdown parameters. 

5.AgentTraces encapsulates the nested outputs of the technical, fundamental, and sentiment agents into a unified object.

6.TF2SynthesisOutput functions as the comprehensive master payload, tracking user IDs, tickers, final recommendations (BUY, HOLD, AVOID, NEUTRAL), confidence scores, reasoning, risk alignment, citations, agent traces, and optional degraded data notices.  


# **Parallel Agent Execution Functions:(TF2)**

1.run_technical_agent prompts the LLM with quantitative market data to evaluate price momentum and generate structured technical signals.

2.run_fundamental_agent processes qualitative document chunks, regulatory disclosures, and filings to extract core fundamental findings. 

3.run_sentiment_agent assesses overarching market context and asset mood to establish sentiment alignment.  


# **Master Orchestration & CIO Synthesis:(TF2)**

1.synthesize_multi_agent initializes the ChatGoogleGenerativeAI wrapper using the gemini-3.6-flash model identifier configured with a temperature setting of 0.2. 

2.It parses asset tickers, quantitative metrics, and qualitative RAG context directly from Task Force 1 input dictionaries. 

3.It concurrently executes all three specialized agent coroutines using asyncio.gather() to minimize processing latency.

4.A master prompt chain casts the LLM as a "Chief Investment Officer and Synthesis Master," balancing independent agent findings against user profile constraints to produce a unified recommendation payload.  


# **File Processing & Execution Pipeline:(TF2)**

1.process_files reads input data from a specified JSON source path and triggers the asynchronous multi-agent pipeline workflow. 

2.It logs runtime execution latency in seconds and populates performance metrics including risk concentration scores and simulated 30-day forward accuracy. 

3.Final outputs are serialized and written to a target JSON file path, defaulting to tf2_output.json. 

4.The script supports direct command-line argument passing or executes default local file workflows (tf1_output.json to tf2_output.json) when invoked independently.



# **Agent Architecture:(TF3)**

The TF3 Financial Intelligence Dashboard is a Streamlit-based web application designed to display autonomous financial intelligence. It serves as an executive interface for real-time multi-agent signal synthesis and portfolio analytics. The application reads synthesized financial data and visualizes actionable insights, execution metrics, and underlying agent reasoning.


1.The application features a sidebar navigation menu with access to the "Dashboard", "Agent Traces", "Risk & Portfolio", and "System Logs".

  i. **Dashboard**: Acts as the primary viewport containing executive summaries, action headers, interactive charts, and the AI assistant.


  ii. **Agent Traces**: Acts as a diagnostic view to inspect the raw JSON outputs of the specialized sub-agents.


  iii. **Risk & Portfolio**: Displays the active behavioral constraints alongside the active user ID and simulated forward performance metrics.


  iv. **System Logs**: Renders the complete, raw JSON payload for deep system inspection.


2.Users can customize a "User Behavioral Profile" by adjusting their Risk Tolerance, Mandate, and Max Drawdown Limit.


3.An execution button allows users to trigger the end-to-end pipeline, which runs tf1_engine.py and tf2_engine.py sequentially using system commands.


4.The main dashboard displays Key Performance Indicators (KPIs), including the Target Asset, Synthesized Signal, Pipeline Latency, and 30D Forward Accuracy.


5.The application generates interactive visualizations using Plotly, specifically a 30-day simulated asset performance line chart and a portfolio risk concentration gauge chart.


6.The interface includes a dedicated section for AI synthesis reasoning, source citations, and warnings for degraded data.


7.An integrated AI Assistant chat interface allows users to ask questions about portfolio balance and recommended actions.
 Application Structure.



#  **Decision & Safety Logic:(TF3)**

1.The application requires several standard and third-party Python libraries: streamlit, json, os, pandas, numpy, and plotly (specifically plotly.graph_objects and plotly.express).


2.To populate the dashboard with data, the application attempts to read from a local file named tf2_output.json.


3.If tf2_output.json is not found, the application falls back to a default state and prompts the user to execute the pipeline.


4.The pipeline execution relies on specific local Python 3.13 paths (/Library/Frameworks/Python.framework/Versions/3.13/bin/python3) to trigger the underlying engine scripts.
