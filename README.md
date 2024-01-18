import pandas as pd
from datetime import datetime

# Création d'un DataFrame pour enregistrer les transactions
transactions = pd.DataFrame(columns=['Date', 'Type', 'Produit', 'Quantité', 'Prix unitaire'])

# Fonction pour ajouter une transaction
def ajouter_transaction(date, transaction_type, produit, quantite, prix_unitaire):
    global transactions
    transactions = pd.concat([transactions, pd.DataFrame({
        'Date': [date],
        'Type': [transaction_type],
        'Produit': [produit],
        'Quantité': [quantite],
        'Prix unitaire': [prix_unitaire]
    })], ignore_index=True)

# Exemple d'utilisation
ajouter_transaction('2024-01-18', 'Achat', 'Produit A', 50, 10.0)
ajouter_transaction('2024-01-18', 'Vente', 'Produit B', 20, 25.0)

# Création d'un DataFrame pour enregistrer les charges
charges = pd.DataFrame(columns=['Date', 'Type', 'Montant'])

# Fonction pour ajouter une charge
def ajouter_charge(date, charge_type, montant):
    global charges
    charges = pd.concat([charges, pd.DataFrame({
        'Date': [date],
        'Type': [charge_type],
        'Montant': [montant]
    })], ignore_index=True)

# Exemple d'utilisation
ajouter_charge('2024-01-18', 'Transport', 100.0)
ajouter_charge('2024-01-18', 'Publicité', 50.0)

# Création d'un DataFrame pour enregistrer les transactions financières (revenus, dépenses)
tresorerie = pd.DataFrame(columns=['Date', 'Type', 'Montant', 'Solde actuel'])

# Fonction pour ajouter une transaction financière
def ajouter_transaction_financiere(date, transaction_type, montant):
    global tresorerie
    solde_precedent = tresorerie['Solde actuel'].iloc[-1] if not tresorerie.empty else 0.0
    tresorerie = pd.concat([tresorerie, pd.DataFrame({
        'Date': [date],
        'Type': [transaction_type],
        'Montant': [montant],
        'Solde actuel': [solde_precedent + montant]
    })], ignore_index=True)

# Exemple d'utilisation
ajouter_transaction_financiere('2024-01-18', 'Revenu', 500.0)
ajouter_transaction_financiere('2024-01-18', 'Dépense', 200.0)

# Calcul des résultats
resultats = pd.DataFrame(columns=['Date', 'Total ventes', 'Coût total achats', 'Total charges', 'Profit'])

resultats.loc[0, 'Date'] = datetime.today().strftime('%Y-%m-%d')
resultats.loc[0, 'Total ventes'] = transactions[transactions['Type'] == 'Vente']['Quantité'].sum()
resultats.loc[0, 'Coût total achats'] = (transactions.loc[transactions['Type'] == 'Achat', 'Quantité'] * transactions.loc[transactions['Type'] == 'Achat', 'Prix unitaire']).sum()
resultats.loc[0, 'Total charges'] = charges['Montant'].sum()
resultats.loc[0, 'Profit'] = resultats.loc[0, 'Total ventes'] - resultats.loc[0, 'Coût total achats'] - resultats.loc[0, 'Total charges']

# Sauvegarde des DataFrames dans un fichier Excel
with pd.ExcelWriter('journal_ecommerçant.xlsx', engine='openpyxl') as writer:
    transactions.to_excel(writer, sheet_name='Transactions', index=False)
    charges.to_excel(writer, sheet_name='Charges', index=False)
    tresorerie.to_excel(writer, sheet_name='Trésorerie', index=False)
    resultats.to_excel(writer, sheet_name='Résultats', index=False)
