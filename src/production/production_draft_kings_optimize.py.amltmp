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

# ... (rest of the code remains the same)

if uploaded_file is not None:
    input_df = pd.read_csv(uploaded_file)

    st.markdown("### Preview of uploaded data:")
    st.write(input_df.head())

    st.markdown("### Set the number of players for each position:")

    # ... (player number inputs)

    # Add cost constraint slider
    cost_constraint = st.slider("Cost Constraint", min_value=0, max_value=100000, value=50000, step=100)

    num_players = {"QB": num_qb, "RB": num_rb, "WR": num_wr, "TE": num_te}

    if st.button("Optimize Lineup"):
        optimal_lineup_df = optimize_draft_kings_lineup(input_df, num_players, cost_constraint)

        st.markdown("### Optimal Lineup:")
        st.write(optimal_lineup_df)

        st.markdown(download_link(optimal_lineup_df, "optimal_lineup.csv", "Download Optimal Lineup as CSV"), unsafe_allow_html=True)
