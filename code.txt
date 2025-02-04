#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_LINE_LENGTH 100  // Maximale Länge einer Zeile in der Datei
#define MAX_ITEMS 100  // Maximale Anzahl von Kategorien
#define MAX_CATEGORY_LENGTH 50  // Maximale Länge eines Kategorienamens

// Struktur zur Speicherung von Kategoriedaten
typedef struct {
    char category[MAX_CATEGORY_LENGTH];
    double total; // Gesamtsumme der Preise
    int count; // Anzahl der Produkte
} CategoryData;

// Gibt den Index der Kategorie zurück, falls gefunden, sonst -1
int findCategoryIndex(CategoryData categories[], int categoryCount, const char *category) {
    for (int i = 0; i < categoryCount; i++) {
        if (strcmp(categories[i].category, category) == 0) { // Vergleich der Kategorienamen
            return i; // Index zurückgeben
        }
    }
    return -1;
}

int main() {
    // Öffnen der Eingabedatei "input.txt"
    FILE *file = fopen("input.txt", "r");
    if (!file) { // Fehlerprüfung, falls die Datei nicht geöffnet werden kann
        printf("Fehler: Datei konnte nicht geöffnet werden.\n");
        return 1; // Mit Fehlercode das Programm beenden
    }

    CategoryData categories[MAX_ITEMS]; // Array zur Speicherung der Kategorien
    int categoryCount = 0; // Anzahl der unterschiedlichen Kategorien
    double grandTotal = 0.0; // Gesamtsumme aller Produkte

    char line[MAX_LINE_LENGTH]; // Buffer für das Einlesen einer Zeile
    while (fgets(line, sizeof(line), file)) { // Lesen der Datei Zeile für Zeile
        strtok(line, "\n"); // Entfernen des Zeilenumbruchs am Ende der Zeile

        char category[MAX_CATEGORY_LENGTH];
        double price; // Variable für den Preis

        // Parsen der Zeile: erwartet "Kategorie Preis"
        if (sscanf(line, "%s %lf", category, &price) != 2) {
            printf("Fehler: Ungültige Zeile: %s\n", line); // Fehlermeldung bei fehlerhaften Einträgen
            continue; // Springe zur nächsten Zeile
        }

        grandTotal += price; // Summe der Preise aktualisieren

        // Überprüfen, ob die Kategorie sich bereits befindet
        int index = findCategoryIndex(categories, categoryCount, category);
        if (index == -1) { // Falls die Kategorie neu ist
            if (categoryCount >= MAX_ITEMS) { // Überprüfung auf maximale Anzahl an Kategorien
                printf("Fehler: Maximale Anzahl an Kategorien erreicht.\n");
                break; // Beenden der Schleife
            }
            // Neue Kategorie hinzufügen
            strcpy(categories[categoryCount].category, category);
            categories[categoryCount].total = price;
            categories[categoryCount].count = 1;
            categoryCount++; // Anzahl der Kategorien erhöhen
        } else { // Falls die Kategorie bereits existiert
            categories[index].total += price; // Preis zur Gesamtsumme der Kategorie hinzufügen
            categories[index].count++; // Produktanzahl in der Kategorie erhöhen
        }
    }

    fclose(file); // Datei schließen

    // Ausgabe der Ergebnisse
    printf("Ausgabe:\n");
    for (int i = 0; i < categoryCount; i++) {
        double average = categories[i].total / categories[i].count; // Durchschnittspreis berechnen
        printf("%s: %.2f, %d Produkte, Mittelwert: %.2f\n",
               categories[i].category,
               categories[i].total,
               categories[i].count,
               average);
    }

    // Gesamtsumme ausgeben
    printf("---------------------\n");
    printf("Summe: %.2f\n", grandTotal);

    return 0; // Programm erfolgreich beenden
}
