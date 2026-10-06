# Career Role Prediction with a Blockchain Ledger

This was my B.Tech final year project at B.S. Abdur Rahman Crescent Institute of Science and Technology, Chennai, completed in May 2024 under the guidance of Dr S. Revathi. The official project title was "ML and Blockchain for Anti-Forgery and Efficiency in Education". I worked on it alone, and the whole project runs in a single Google Colab notebook.

## What it does

1. Takes 25 attributes about a student, such as school marks band, CGPA band, number of projects, skill ratings and symposium participation
2. Predicts which of 8 job roles suits them: Business Analyst, Data Analyst, Software Developer, Software Tester, Technical Support, Technical Writer, UI/UX Designer or Web Developer
3. Stores every prediction in a simple blockchain-style ledger, where each record is linked to the previous one with a SHA-256 hash, so an edited record can be detected

## How the notebook works

1. **Exploring the data.** A ydata-profiling report and AutoViz charts.
2. **Encoding.** The 8 roles are label-encoded to numbers.
3. **Feature selection.** SelectKBest with the chi-squared test scores each attribute against the role. Attributes scoring above 1 are kept, which leaves 19 of the 25.
4. **Models.** An SVM and a Decision Tree are trained. The Decision Tree makes the predictions in the final step.
5. **Ledger.** A `Block` class stores an index, a timestamp, the student record with its prediction and the previous block's hash, and computes its own SHA-256 hash. A `Blockchain` class starts with a genesis block, links every new block to the last one, can check the whole chain with `is_chain_valid()`, and saves itself to `blockchain.json`.
6. **Prediction loop.** You type in a student's 25 attributes, the Decision Tree predicts a role, and the record is added to the chain and to `updated.csv`.

## How tampering is detected

Each block's hash is calculated from its own contents and the previous block's hash. If someone changes an old record, that block's hash no longer matches, and the next block's link breaks too. `is_chain_valid()` recalculates every hash and checks every link.

This is a simplified, single-machine ledger. There is no network of nodes, no consensus and no proof of work, so it can show that a record was changed, but it cannot stop someone who rebuilds the whole chain. A real system would keep the latest hash somewhere the editor cannot change.

## Limitations

- **Very little labelled data.** The dataset has 7,525 rows, but only 49 have a job role filled in, so the models were trained on 49 rows and tested on 10 to 15 rows. One prediction changes the accuracy by 7 to 10 percentage points, so I do not treat the accuracy figures as meaningful.
- **XGBoost was never properly evaluated.** The notebook trains an XGBoost model, but the scoring cell uses the Decision Tree's predictions by mistake.
- **No forgery detection model.** The "anti-forgery" part of the title refers to the tamper-evident ledger.
- No input validation: values outside the training ranges are accepted.

## How to run

Open `Career_Recommendation.ipynb` in Google Colab, upload `sample_data.csv` to `/content` using the Colab file panel, and run the cells in order. The dataset is not included in this repository.

## Tools used

Python, pandas, NumPy, scikit-learn, XGBoost, Matplotlib, seaborn, ydata-profiling, AutoViz, hashlib, JSON.

## What I would do differently now

- Collect much more labelled data before training any model
- Use cross-validation and compare all models on the same split
- Fix the XGBoost evaluation
- Validate the input values
- Anchor the latest hash of the chain somewhere trusted
