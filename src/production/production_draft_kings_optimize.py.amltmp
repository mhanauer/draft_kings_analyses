import streamlit as st
import pandas as pd
import base64
import pulp
import pandas as pd

def optimize_draft_kings_lineup(df, num_players):
    # Create the problem variable
    prob = pulp.LpProblem("DraftKings_Milly_Makers", pulp.LpMaximize)

    # Create decision variables
    player_vars = pulp.LpVariable.dicts("Player", (player for player in df["name"]), 0, 1, pulp.LpInteger)

    # Objective function: maximize total value
    prob += pulp.lpSum([df.loc[df["name"] == player, "value"].values[0] * player_vars[player] for player in df["name"]]), "Total_Value"

    # Constraints
    prob += pulp.lpSum([df.loc[df["name"] == player, "cost"].values[0] * player_vars[player] for player in df["name"]]) <= 50000, "Total_Cost"
    
    for position, count in num_players.items():
        prob += pulp.lpSum([player_vars[player] for player in df[df["position"] == position]["name"]]) == count, f"{count}_{position}s"

    # Solve the problem
    prob.solve()

    # Store the results
    optimal_lineup = [v.name.split("_")[1] for v in prob.variables() if v.varValue > 0]

    # Return the optimal lineup as a DataFrame
    return df[df["name"].isin(optimal_lineup)]



def download_link(df, filename, text):
    csv = df.to_csv(index=False)
    b64 = base64.b64encode(csv.encode()).decode()
    href = f'<a href="data:file/csv;base64,{b64}" download="{filename}" target="_blank">{text}</a>'
    return href


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

    if st.button("Optimize Lineup"):
        optimal_lineup_df = optimize_draft_kings_lineup(input_df, num_players)

        st.markdown("### Optimal Lineup:")
        st.write(optimal_lineup_df)

        st.markdown(download_link(optimal_lineup_df, "optimal_lineup.csv", "Download Optimal Lineup as CSV"), unsafe_allow_html=True)
