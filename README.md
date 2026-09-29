# personal-expence-tracker
•  Records date, amount, category, and payment method. • Categorization: Organizes spend into groups like food, rent, and utilities. • Reporting: Generates daily, monthly, or yearly spending summaries. • Budgeting Dashboard: Visualizes cash flow and remaining funds via charts.
import pandas as pd
import streamlit as st

st.set_page_config(page_title="💲Personal Expense Tracker",  layout="wide")


if "expenses" not in st.session_state:
  st.session_state.expenses = pd.DataFrame(
      columns=["Date", "Category", "Amount", "Description"]
  )

st.title(" Personal Expense Tracker")
st.markdown("Track your daily spending and analyze your budget instantly.")


st.sidebar.header(" Add New Expense")
with st.sidebar.form("expense_form", clear_on_submit=True):
  date = st.date_input("Date")
  category = st.selectbox(
      "Category", ["Food", "Transport", "Shopping"]
  )
  amount = st.number_input("Amount (₹)", min_value=0.0, step=50.0)
  description = st.text_input("Description (Optional)")
  submitted = st.form_submit_button("Add Expense")

  if submitted:
    new_data = pd.DataFrame({
        "Date": [pd.to_datetime(date)],
        "Category": [category],
        "Amount": [amount],
        "Description": [description],
    })
    st.session_state.expenses = pd.concat(
        [st.session_state.expenses, new_data], ignore_index=True
    )
    st.sidebar.success("Expense added successfully!")

if not st.session_state.expenses.empty:
 
  total_spent = st.session_state.expenses["Amount"].sum()
  col1, col2 = st.metric("Total Spent", f"₹{total_spent:,.2f}"), st.metric(
      "Total Transactions", len(st.session_state.expenses)
  )

  st.subheader("Spending by Category")
  category_summary = (
      st.session_state.expenses.groupby("Category")["Amount"]
      .sum()
      .reset_index()
  )
  st.bar_chart(category_summary.set_index("Category"))

  st.subheader(" Transaction History")
