===========================================
  GREENLOOM CORPORATE PORTAL — README
  Sustainability & Carbon Compliance AI Platform
  Malaysian Carbon Capture, Utilisation and Storage (CCUS) Act 2025
===========================================
--------------------------------------------------------------------------------
10 Minutes Pitching Video
--------------------------------------------------------------------------------

Click here: 

--------------------------------------------------------------------------------
FILES IN THIS PROJECT
--------------------------------------------------------------------------------

  chatbox3d.py               — Main application (run this file)
  Data_extract.py           — Standalone PDF extraction utility (legacy helper)
  chat_data.json            — Chat messages and user credentials
  green_loom_production.db  — SQLite database (auto-created on first run)
  knowledge_base/           — Folder for policy PDF uploads (auto-created)
  submission_report/        — Folder for generated PDF reports (auto-created)

--------------------------------------------------------------------------------
SYSTEM REQUIREMENTS
--------------------------------------------------------------------------------

  Python           3.9 or higher
  Operating System Windows / macOS / Linux

  Required Python packages:

    streamlit
    pdfplumber
    fpdf2
    requests
    plotly
    zhipuai

  Install all dependencies with:

    pip install streamlit pdfplumber fpdf2 requests plotly zhipuai

--------------------------------------------------------------------------------
HOW TO RUN
--------------------------------------------------------------------------------
 Option 1:
    Run with our hosted website:
    https://greenloom.streamlit.app/

Option 2:
    Run on local environment:

  1. Place all project files in the same folder:
       chatbox3d.py
       chat_data.json
       green_loom_production.db

  2. Git clone:
       https://github.com/guirong0109/greenloom.git
       Require own API key for ilmu-glm-5.1 and groq 
       In .env.example file, replace the API key 
       Rename the file as .env 

  3. Open a terminal in that folder and run:

       streamlit run chatbox3d.py

  4. A browser window will open automatically at:

       http://localhost:8501

  5. Log in with one of the default user accounts (see USERS section below).

--------------------------------------------------------------------------------
USER ACCOUNTS & ROLES
--------------------------------------------------------------------------------

  The system has 4 pre-loaded user accounts defined in chat_data.json.
  Each account has a role that controls which features are visible.

  Username  PIN      Role
  --------  -------  --------
  Yip       abc123   CEO
  Chow      IU       Manager
  Tan       gg       Worker
  Jane      hi       Worker

  ROLE PERMISSIONS:

  CEO (Yip)
    - Full access to all features
    - Can view the Dashboard, Carbon Calculator, Procurement Report, and Chat
    - Can upload and manage Policy PDFs
    - Can view DB statistics in the sidebar

  Manager (Chow)
    - Access to Dashboard, Carbon Calculator, and Chat
    - Cannot upload policy PDFs
    - Cannot generate procurement reports

  Worker (Tan, Jane)
    - Access to Dashboard and Chat only
    - Cannot access the Carbon Calculator or report generation

--------------------------------------------------------------------------------
APPLICATION FEATURES
--------------------------------------------------------------------------------

1. DASHBOARD (All roles)
   ----------------------
   The default landing page after login. Shows:

   - 5 KPI cards: Total Emissions, Scope 1, Scope 2, Scope 3, Carbon Tax
   - Emission Breakdown: horizontal bar chart for each emission category
   - Tax Summary Box: gross tax, offsets applied, net payable
   - Scope Distribution: donut chart showing Scope 1/2/3 proportions
   - Submission History: last 6 carbon calculator submissions with tax status

   Data is pulled live from the SQLite database on every load. If no
   submissions exist yet, a prompt appears to use the Carbon Calculator.


2. OPERATIONS CHAT (All roles)
   ----------------------------
   A shared team messaging panel toggled via "Open Chat" in the sidebar.

   - All users can read and send messages
   - Messages are saved to chat_data.json in real time
   - Type @AI in any message to get an instant AI reply from GreenLoom AI
   - AI responses are grounded on the LCA database and knowledge base PDFs
   - Materials mentioned in chat (e.g. "15,000,000kg of structural steel")
     are automatically extracted and sent to the Carbon Calculator


3. GENERATE REPORT (CEO only, from Chat panel)
   ---------------------------------------------
   Click "Generate Report" while the chat panel is open to trigger a full
   procurement analysis using ILMU-GLM AI.

   The report covers:
   - All procurement items discussed in the chat
   - Option-by-option comparison (cost, carbon, delivery, ESG impact)
   - Carbon tax calculation per option using the CCUS Act 2025 rate
   - CEO recommendation with LCA database citations
   - A JSON block of materials auto-transferred to the Carbon Calculator

   After generation, the report is viewable under "View Last Report" in
   the sidebar. Materials extracted from the report are automatically
   saved to the Scope 3 Materials section of the Carbon Calculator.


4. CARBON TAX CALCULATOR (CEO + Manager)
   ----------------------------------------
   A step-by-step GHG emission calculator based on the GHG Protocol.
   Accessible via the sidebar or the "Calculator" button on the Dashboard.


   SCOPE 1 — Direct Emissions (Fuel)
     Input petrol, diesel, or LPG consumption. Upload receipt images
     (JPG/PNG) to have Groq Vision AI extract the litre amounts automatically.
     Emission factors: Petrol 2.31 | Diesel 2.68 | LPG 3.02 kg CO2e/unit
     
     *Images prepared for uploading in folder "petrol receipt"

   SCOPE 2 — Indirect Emissions (Electricity)
     Input monthly electricity consumption in kWh. Upload TNB bill images
     for automatic kWh extraction via Groq Vision AI.
     Emission factor: Malaysia grid 0.694 kg CO2e/kWh

     *Images prepared for uploading in folder "electricity bill"

   SCOPE 3 — Logistics & Supplier
     Select from 4 pre-loaded suppliers (Shah Alam, Klang, Johor Bahru,
     Penang) or enter custom distance. Distance and fuel are auto-filled
     based on route. Upload logistics receipts for AI extraction.
     Log trips to build a Supplier Carbon Comparison table.
     Click "Get AI Supplier Recommendation" for the lowest-carbon option.

   SCOPE 3 — Waste Generated in Operations
     Input solid waste, scrap metal, wastewater, and general waste.
     Upload waste photos to use the Groq Vision AI "Wastage Identify"
     feature — AI estimates the waste type and weight from the image.
     Confidence percentage is shown (green ≥70%, orange ≥40%, red <40%).

     *Images prepared for uploading in folder "wastage"


   SCOPE 3 — Purchased Goods & Materials (Category 1)
     Materials detected from chat messages are auto-imported here.
     Manual rows can also be added using the 40+ material dropdown with
     cradle-to-gate emission factors (IPCC / ecoinvent references).
     An emission factor reference table is available as an expandable view.
     Import button: "Import Materials from Chat History" scans the full
     chat and loads all detected materials into the calculator.

   OFFSETS
     Enter voluntary carbon credits purchased. Cost assumed at RM 20/tonne.
     Offsets are deducted before the free allowance threshold is applied.

   TAX CALCULATION
     Free allowance: 24,000 tonnes CO2e (no tax below this)
     Tax rate: RM 15 per tonne CO2e (CCUS Act 2025)
     Formula: max(0, Total - Offsets - 24,000t) × RM 15

   SAVE SUBMISSION
     Click "Save Submission" to store the result in the database and
     generate a PDF report saved to the submission_report/ folder.
     PDF reports can be downloaded or deleted from within the calculator.


5. POLICY MANAGEMENT (CEO only)
   -------------------------------
   Accessible via "Policy Upload" in the sidebar.

   - Upload new policy PDFs (e.g. CCUS Act, HS Code tables, LCA studies)
   - PDFs are saved to the knowledge_base/ folder and ingested into SQLite
   - Tables are extracted for structured data (HS Codes, prices)
   - Text is extracted for policy and regulatory content
   - "Regenerate Full Dataset" re-processes all PDFs in the folder
   - Ingested content is used by GreenLoom AI when answering @AI queries

--------------------------------------------------------------------------------
AI ENGINES USED
--------------------------------------------------------------------------------

  GreenLoom AI (Procurement & Compliance Analysis)
    Model  : ilmu-glm-5.1
    Endpoint: https://api.ilmu.ai/v1/chat/completions
    Used for: @AI chat replies, Generate Report, Supplier Recommendation
    Key    : Configured in chatbox3d.py (Z_AI_API_KEY)

  Groq Vision AI (Receipt & Waste Image Extraction)
    Model  : meta-llama/llama-4-scout-17b-16e-instruct
    Endpoint: https://api.groq.com/openai/v1/chat/completions
    Used for: Petrol/diesel/electricity receipt OCR, waste identification
    Key    : Configured in chatbox3d.py (GROQ_API_KEY)

  NOTE: Both API keys are pre-configured in chatbox3d.py. Do not share these
  keys publicly. Rotate them if the application is deployed to production.

--------------------------------------------------------------------------------
DATABASE STRUCTURE (green_loom_production.db)
--------------------------------------------------------------------------------

  Table: materials
    item          TEXT    — Material name
    price         REAL    — Quantity in kg (column named 'price' for legacy reasons)
    carbon_factor REAL    — Emission factor in kg CO2e per kg
    hs_code       TEXT    — HS tariff code if available

  Table: lca_metrics
    material      TEXT    — Material name (primary key)
    ghg_impact    TEXT    — GHG rating description
    energy_saving REAL    — Non-renewable energy saving percentage
    cost_advantage REAL   — Lifecycle cost advantage percentage

  Table: knowledge_base
    source        TEXT    — Source PDF filename
    page_num      INTEGER — Page number within the PDF
    content       TEXT    — Extracted text or table row content

  Table: carbon_submissions
    id            INTEGER — Auto-increment primary key
    submitted_by  TEXT    — Username who submitted
    submitted_at  TEXT    — ISO timestamp
    scope1_co2e   REAL    — Scope 1 emissions in tonnes
    scope2_co2e   REAL    — Scope 2 emissions in tonnes
    scope3_co2e   REAL    — Scope 3 total in tonnes
    scope3_logistics_co2e REAL
    scope3_waste_co2e     REAL
    total_co2e    REAL    — Total emissions in tonnes
    taxable_co2e  REAL    — Taxable tonnes after free allowance
    carbon_tax_rm REAL    — Gross carbon tax in RM
    offset_co2e   REAL    — Voluntary offsets applied in tonnes
    net_tax_rm    REAL    — Net tax payable after offsets
    supplier_used TEXT    — Supplier selected for that submission
    notes         TEXT    — Optional submission notes

  Table: supplier_carbon_log
    id            INTEGER — Auto-increment primary key
    logged_at     TEXT    — ISO timestamp
    supplier_name TEXT
    trips         INTEGER — Number of trips made
    distance_km   REAL    — Total distance
    diesel_litres REAL    — Total fuel consumed
    co2e_kg       REAL    — Total emissions in kg
    cost_rm       REAL    — Estimated carbon tax cost

--------------------------------------------------------------------------------
EMISSION FACTORS REFERENCE
--------------------------------------------------------------------------------

  SCOPE 1 (Direct)
    Petrol       2.31 kg CO2e / litre
    Diesel       2.68 kg CO2e / litre
    LPG          3.02 kg CO2e / kg

  SCOPE 2 (Electricity)
    Malaysia grid  0.694 kg CO2e / kWh

  SCOPE 3 LOGISTICS
    Distance-based  0.171 kg CO2e / km
    Diesel (truck)  2.68  kg CO2e / litre

  SCOPE 3 WASTE
    Solid waste      0.50 kg CO2e / kg
    Scrap metal      1.46 kg CO2e / kg
    Wastewater       0.42 kg CO2e / m3
    General waste    0.50 kg CO2e / kg

  SCOPE 3 MATERIALS (selected, cradle-to-gate)
    Steel (virgin/primary)    1.91 kg CO2e / kg
    Steel (recycled)          0.43 kg CO2e / kg
    Concrete                  0.13 kg CO2e / kg
    Cement                    0.89 kg CO2e / kg
    Aluminium (virgin)       11.89 kg CO2e / kg
    Aluminium (recycled)      0.65 kg CO2e / kg
    PVC                       2.41 kg CO2e / kg
    (Full table of 40+ materials available in the calculator)

--------------------------------------------------------------------------------
CARBON TAX FORMULA (CCUS ACT 2025)
--------------------------------------------------------------------------------

  Step 1: Calculate total emissions
          Total (t) = Scope1 + Scope2 + Scope3

  Step 2: Deduct voluntary offsets
          Net (t) = Total - Offsets

  Step 3: Deduct free allowance
          Taxable (t) = max(0, Net - 24,000)

  Step 4: Apply tax rate
          Gross Tax (RM) = Taxable × 15

  Step 5: Deduct offset purchase cost
          Net Tax (RM) = max(0, Gross Tax - (Offsets × 20))

--------------------------------------------------------------------------------
TESTING THE CARBON CALCULATOR (EXCEEDING TAX THRESHOLD)
--------------------------------------------------------------------------------

  The free allowance is 24,000 tonnes. To test a visible carbon tax bill,
  enter the following values in the Carbon Calculator:

  Scope 1 — Diesel: 7,500,000 litres
    → 7,500,000 × 2.68 = 20,100 t CO2e

  Scope 3 — Materials: Import from Chat
    (The chat_data.json already contains materials that contribute ~3,000+ t)

  Expected result:
    Total > 24,000 t → Taxable amount > 0 → Tax bill appears in RM

  Alternatively, use the "Import Materials from Chat History" button after
  loading the provided chat_data.json, then add Scope 1 diesel manually.

--------------------------------------------------------------------------------
ADDING NEW USERS
--------------------------------------------------------------------------------

  Open chat_data.json and add entries under the "users" key:

  "NewUser": { "pin": "yourpin", "role": "Worker" }

  Valid roles: "CEO", "Manager", "Worker"

--------------------------------------------------------------------------------
KNOWN LIMITATIONS
--------------------------------------------------------------------------------

  - The materials table uses the column name "price" to store qty_kg.
    This is a legacy schema kept for compatibility. Do not rename this column.

  - The 24,000 tonne free allowance is a simplified figure for demonstration.
    Actual CCUS Act 2025 thresholds may differ by industry and facility type.

  - Groq Vision receipt extraction works best with clear, well-lit photos.
    Handwritten receipts may produce inaccurate results.

  - Chat data is stored in chat_data.json as plain text. For production
    deployment, migrate to a proper database with authentication.

  - API keys are hardcoded in chatbox3d.py. Use environment variables for
    any production or public deployment.

--------------------------------------------------------------------------------
BUILT FOR
--------------------------------------------------------------------------------

  Malaysia University Hackathon (M-U-X)
  Track: ESG & Carbon Compliance
  AI Engine: ILMU-GLM 5.1 (ilmu.ai)

===========================================
  END OF README
===========================================
