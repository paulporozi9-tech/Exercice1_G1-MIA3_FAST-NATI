# Exercice1_G1-MIA3_FAST-NATI
Une bibliothèque souhaite numérisé la gestion de ces livres de ces étudiants 
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>

using namespace std;

enum class EtatLivre { DISPONIBLE, EMPRUNTE };

class Livre {
private:
    int id;
    string titre;
    string auteur;
    int anneePublication;
    EtatLivre etat;

public:
    Livre(int id, string titre, string auteur, int anneePublication)
        : id(id), titre(titre), auteur(auteur),
          anneePublication(anneePublication), etat(EtatLivre::DISPONIBLE) {}

    int getId() const { return id; }
    const string& getTitre() const { return titre; }
    EtatLivre getEtat() const { return etat; }
    bool estDisponible() const { return etat == EtatLivre::DISPONIBLE; }

    bool emprunter() {
        if (!estDisponible()) return false;
        etat = EtatLivre::EMPRUNTE;
        return true;
    }

    void retourner() { etat = EtatLivre::DISPONIBLE; }
};

class Etudiant {
private:
    string matricule;
    string nom;
    string prenom;
    string email;
    vector<int> livresEmpruntes;

public:
    Etudiant(string matricule, string nom, string prenom, string email)
        : matricule(matricule), nom(nom), prenom(prenom), email(email) {}

    const string& getMatricule() const { return matricule; }
    string getNomComplet() const { return prenom + " " + nom; }

    void ajouterEmprunt(int idLivre) { livresEmpruntes.push_back(idLivre); }

    void retirerEmprunt(int idLivre) {
        livresEmpruntes.erase(
            remove(livresEmpruntes.begin(), livresEmpruntes.end(), idLivre),
            livresEmpruntes.end());
    }
};

class Bibliotheque {
private:
    vector<Livre> livres;
    vector<Etudiant> etudiants;

    Livre* trouverLivre(int id) {
        for (auto& livre : livres)
            if (livre.getId() == id) return &livre;
        return nullptr;
    }

    Etudiant* trouverEtudiant(const string& matricule) {
        for (auto& etudiant : etudiants)
            if (etudiant.getMatricule() == matricule) return &etudiant;
        return nullptr;
    }

public:
    void ajouterLivre(const Livre& livre) { livres.push_back(livre); }
    void inscrireEtudiant(const Etudiant& etudiant) { etudiants.push_back(etudiant); }

    bool emprunterLivre(const string& matricule, int idLivre) {
        Etudiant* etudiant = trouverEtudiant(matricule);
        Livre* livre = trouverLivre(idLivre);

        if (etudiant == nullptr || livre == nullptr || !livre->emprunter())
            return false;

        etudiant->ajouterEmprunt(idLivre);
        return true;
    }

    bool retournerLivre(const string& matricule, int idLivre) {
        Etudiant* etudiant = trouverEtudiant(matricule);
        Livre* livre = trouverLivre(idLivre);

        if (etudiant == nullptr || livre == nullptr || livre->estDisponible())
            return false;

        livre->retourner();
        etudiant->retirerEmprunt(idLivre);
        return true;
    }
};

int main() {
    Bibliotheque bibliotheque;

    bibliotheque.ajouterLivre(
        Livre(1, "UML et conception objet", "Auteur Exemple", 2024));
    bibliotheque.inscrireEtudiant(
        Etudiant("ETU001", "Dupont", "Alice", "alice@universite.fr"));

    if (bibliotheque.emprunterLivre("ETU001", 1))
        cout << "Emprunt effectue avec succes." << endl;
    else
        cout << "Emprunt impossible." << endl;

    if (bibliotheque.retournerLivre("ETU001", 1))
        cout << "Retour effectue avec succes." << endl;
    else
        cout << "Retour impossible." << endl;

    return 0;
}
