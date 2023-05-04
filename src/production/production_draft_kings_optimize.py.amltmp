import streamlit as st
import pandas as pd
import base64
import pulp
import pandas as pd

def optimize_draft_kings_lineup(df, num_players, cost_constraint):
    # Create the problem variable
    prob = pulp.LpProblem("DraftKings_Milly_Makers", pulp.LpMaximize)

    # Create decision variables
    player_vars = pulp.LpVariable.dicts("Player", (player for player in df["name"]), 0, 1, pulp.LpInteger)

    # Objective function: maximize total value
    prob += pulp.lpSum([df.loc[df["name"] == player, "value"].values[0] * player_vars[player] for player in df["name"]]), "Total_Value"

    # Constraints
    prob += pulp.lpSum([df.loc[df["name"] == player, "cost"].values[0] * player_vars[player] for player in df["name"]]) <= cost_constraint, "Total_Cost"
    
    for position, count in num_players.items():
        prob += pulp.lpSum([player_vars[player] for player in df[df["position"] == position]["name"]]) == count, f"{count}_{position}s"

    # Solve the problem
    prob.solve()

    # Store the results
    optimal_lineup = [v.name.split("_")[1] for v in prob.variables() if v.varValue > 0]

    # Return the optimal lineup as a DataFrame
    return df[df["name"].isin(optimal_lineup)]

st.title("DraftKings Milly Makers Lineup Optimizer")

st.markdown("""
Upload a CSV file containing player data with columns "name", "position", "cost", and "value".
Set the number of players for each position, and the app will optimize the lineup.
""")

uploaded_file = st.file_uploader("Upload player data (CSV)", type="csv")

if uploaded_file is not None:
    input_df = pd.read_csv(uploaded_file)

    st.markdown("### Preview of uploaded data:")
    st.write(input_df.head())

    st.markdown("### Set the number of players for each position:")

    num_qb = st.number_input("Number of Quarterbacks (QB)", min_value=0, value=1)
    num_rb = st.number_input("Number of Running Backs (RB)", min_value=0, value=2)
    num_wr = st.number_input("Number of Wide Receivers (WR)", min_value=0, value=3)
    num_te = st.number_input("Number of Tight Ends (TE)", min_value=0, value=1)

    num_players = {"QB": num_qb, "RB": num_rb, "WR": num_wr, "TE": num_te}

    cost_constraint = st.slider("Cost Constraint ($)", min_value=0, max_value=100000, value=50000, step=1000)

    if st.button("Optimize Lineup"):
        optimal_lineup_df = optimize_draft_kings_lineup(input_df, num_players, cost_constraint)

        st.markdown("### Optimal Lineup:")
        st.write(optimal_lineup_df)

        st.markdown(download_link(optimal_lineup_df, "optimal_lineup.csv", "Download Optimal Lineup as CSV"), unsafe_allow_html=True)
