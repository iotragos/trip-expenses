const people = [
    "Tragoulias",
    "Ntalakas",
    "Tzoumkas",
    "Fygetakis",
    "Kanellos Nikos",
    "Kanellos Vasilis",
    "Satolias",
    "Tziallas"
];

let expenses = JSON.parse(localStorage.getItem("expenses")) || [];

const peopleDiv = document.getElementById("people");
const payerSelect = document.getElementById("payer");
const expensesDiv = document.getElementById("expenses");
const balancesDiv = document.getElementById("balances");


// Load people
people.forEach(person => {

    let div = document.createElement("div");
    div.className = "person";
    div.innerHTML = person;

    peopleDiv.appendChild(div);


    let option = document.createElement("option");
    option.value = person;
    option.textContent = person;

    payerSelect.appendChild(option);
});


// Add expense
function addExpense() {

    const description =
        document.getElementById("description").value;

    const amount =
        Number(document.getElementById("amount").value);

    const payer =
        document.getElementById("payer").value;


    if (!description || !amount) {
        alert("Please enter description and amount");
        return;
    }


    expenses.push({
        description,
        amount,
        payer,
        date: new Date().toLocaleDateString()
    });


    saveExpenses();

    document.getElementById("description").value = "";
    document.getElementById("amount").value = "";

    render();
}


// Save
function saveExpenses() {
    localStorage.setItem(
        "expenses",
        JSON.stringify(expenses)
    );
}


// Show expenses
function renderExpenses() {

    expensesDiv.innerHTML = "";

    if (expenses.length === 0) {
        expensesDiv.innerHTML = "No expenses yet.";
        return;
    }


    expenses.forEach((expense, index) => {

        let div = document.createElement("div");

        div.className = "expense";

        div.innerHTML = `
        <b>${expense.description}</b><br>
        €${expense.amount.toFixed(2)}
        paid by ${expense.payer}
        <br>
        ${expense.date}
        <br>
        <button onclick="deleteExpense(${index})">
        Delete
        </button>
        `;

        expensesDiv.appendChild(div);

    });

}


// Delete expense
function deleteExpense(index) {

    expenses.splice(index, 1);

    saveExpenses();

    render();

}


// Calculate balances
function renderBalances() {

    let balance = {};

    people.forEach(person => {
        balance[person] = 0;
    });


    expenses.forEach(expense => {

        let share =
            expense.amount / people.length;


        balance[expense.payer] += expense.amount;


        people.forEach(person => {
            balance[person] -= share;
        });

    });


    balancesDiv.innerHTML = "";


    people.forEach(person => {

        let value =
            balance[person];


        let div =
            document.createElement("div");


        if (value >= 0) {

            div.className = "balance-positive";

            div.innerHTML =
            `${person} should receive €${value.toFixed(2)}`;

        } else {

            div.className = "balance-negative";

            div.innerHTML =
            `${person} owes €${Math.abs(value).toFixed(2)}`;

        }


        balancesDiv.appendChild(div);

    });

}


// Render everything
function render() {

    renderExpenses();

    renderBalances();

}


render();
