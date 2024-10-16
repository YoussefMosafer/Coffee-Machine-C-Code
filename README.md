#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#define ESPRESSO_BEANS 8
#define ESPRESSO_WATER 30
#define ESPRESSO_MILK 0
#define ESPRESSO_SYRUP 0
#define ESPRESSO_PRICE_DEFAULT 3.5

#define CAPPUCCINO_BEANS 8
#define CAPPUCCINO_WATER 30
#define CAPPUCCINO_MILK 70
#define CAPPUCCINO_SYRUP 0
#define CAPPUCCINO_PRICE_DEFAULT 4.5

#define MOCHA_BEANS 8
#define MOCHA_WATER 39
#define MOCHA_MILK 160
#define MOCHA_SYRUP 30
#define MOCHA_PRICE_DEFAULT 5.5

#define HOT_WATER_WATER 50
#define HOT_WATER_PRICE 1.0

#define LOW_THRESHOLD_BEANS 10
#define LOW_THRESHOLD_WATER 50
#define LOW_THRESHOLD_MILK 80
#define LOW_THRESHOLD_SYRUP 20

#define ADMIN_PASSWORD 1234

float coffee_beans = 100.0;
float water = 500.0;
float milk = 300.0;
float syrup = 100.0;

float espresso_price = ESPRESSO_PRICE_DEFAULT;
float cappuccino_price = CAPPUCCINO_PRICE_DEFAULT;
float mocha_price = MOCHA_PRICE_DEFAULT;

float total_amount = 0.0;

void displayMenu();
void orderCoffee();
void adminMode();
void checkIngredients();
void displayIngredientsAndSales();
void replenishIngredients();
void changeCoffeePrice();
void resetSalesAmount();

int main() {
    int choice;
    while (1) {
        displayMenu();
        scanf("%d", &choice);

        switch (choice) {
            case 1:
                orderCoffee();
                break;
            case 2:
                adminMode();
                break;
            case 3:
                printf("Application Ended.\n");
                return 0;
            default:
                printf("Invalid choice! Please choose again.\n");
        }
    }
    return 0;
}

void displayMenu() {
    printf("\n---- Coffee Machine Menu ----\n");
    printf("1. Order a coffee\n");
    printf("2. Admin mode\n");
    printf("3. Exit\n");
    printf("Choose an option: ");
}

void orderCoffee() {
    int selection;
    float price = 0;
    char confirm;
    float payment = 0;
    float change = 0;

    while (1) {
        printf("\n---- Select Coffee ----\n");
        if (coffee_beans >= ESPRESSO_BEANS && water >= ESPRESSO_WATER) {
            printf("1. Espresso (AED %.2f)\n", espresso_price);
        } else {
            printf("1. Espresso (Unavailable due to temporarily insufficient ingredients)\n");
        }
        if (coffee_beans >= CAPPUCCINO_BEANS && water >= CAPPUCCINO_WATER && milk >= CAPPUCCINO_MILK) {
            printf("2. Cappuccino (AED %.2f)\n", cappuccino_price);
        } else {
            printf("2. Cappuccino (Unavailable due to temporarily insufficient ingredients)\n");
        }
        if (coffee_beans >= MOCHA_BEANS && water >= MOCHA_WATER && milk >= MOCHA_MILK && syrup >= MOCHA_SYRUP) {
            printf("3. Mocha (AED %.2f)\n", mocha_price);
        } else {
            printf("3. Mocha (Unavailable due to temporarily insufficient ingredients)\n");
        }
        if (water >= HOT_WATER_WATER) {
            printf("4. Hot Water (AED %.2f)\n", HOT_WATER_PRICE);
        } else {
            printf("4. Hot Water (Unavailable due to temporarily insufficient ingredients)\n");
        }
        printf("0. Cancel\n");
        printf("Enter your choice: ");
        scanf("%d", &selection);

        switch (selection) {
            case 1:
                if (coffee_beans >= ESPRESSO_BEANS && water >= ESPRESSO_WATER) {
                    price = espresso_price;
                    printf("You selected: Espresso (AED %.2f)\n", price);
                } else {
                    printf("Espresso is unavailable due to insufficient ingredients.\n");
                    continue;
                }
                break;
            case 2:
                if (coffee_beans >= CAPPUCCINO_BEANS && water >= CAPPUCCINO_WATER && milk >= CAPPUCCINO_MILK) {
                    price = cappuccino_price;
                    printf("You selected: Cappuccino (AED %.2f)\n", price);
                } else {
                    printf("Cappuccino is unavailable due to insufficient ingredients.\n");
                    continue;
                }
                break;
            case 3:
                if (coffee_beans >= MOCHA_BEANS && water >= MOCHA_WATER && milk >= MOCHA_MILK && syrup >= MOCHA_SYRUP) {
                    price = mocha_price;
                    printf("You selected: Mocha (AED %.2f)\n", price);
                } else {
                    printf("Mocha is unavailable due to insufficient ingredients.\n");
                    continue;
                }
                break;
            case 4:
                if (water >= HOT_WATER_WATER) {
                    price = HOT_WATER_PRICE;
                    printf("You selected: Hot Water (AED %.2f)\n", price);
                } else {
                    printf("Hot Water is unavailable due to insufficient ingredients.\n");
                    continue;
                }
                break;
            case 0:
                return;
            default:
                printf("Invalid selection! Please choose again.\n");
                continue;
        }

        printf("Confirm your order? (y/n): ");
        scanf(" %c", &confirm);
        if (confirm != 'y') {
            continue;
        }

        payment = 0;
        while (payment < price) {
            float coin;
            printf("Insert coin (AED 1 or AED 0.5): ");
            scanf("%f", &coin);
            if (coin == 1 || coin == 0.5) {
                payment += coin;
            } else {
                printf("Invalid coin! Please collect it and insert a valid one.\n");
            }
        }

        change = payment - price;
        printf("You bought a drink for AED %.2f. Paid: AED %.2f, Change: AED %.2f\n", price, payment, change);

        if (selection == 1) {
            coffee_beans -= ESPRESSO_BEANS;
            water -= ESPRESSO_WATER;
        } else if (selection == 2) {
            coffee_beans -= CAPPUCCINO_BEANS;
            water -= CAPPUCCINO_WATER;
            milk -= CAPPUCCINO_MILK;
        } else if (selection == 3) {
            coffee_beans -= MOCHA_BEANS;
            water -= MOCHA_WATER;
            milk -= MOCHA_MILK;
            syrup -= MOCHA_SYRUP;
        } else if (selection == 4) {
            water -= HOT_WATER_WATER;
            printf("Preparing Hot Water...\n");
        }

        total_amount += price;
        checkIngredients();
        return;
    }
}

void adminMode() {
    int password;
    printf("Enter admin password: ");
    scanf("%d", &password);

    if (password == ADMIN_PASSWORD) {
        int choice;
        while (1) {
            printf("\n---- Admin Mode ----\n");
            printf("1. Display ingredients and total sales\n");
            printf("2. Replenish ingredients\n");
            printf("3. Change coffee price\n");
            printf("0. Exit Admin Mode\n");
            printf("Choose an option: ");
            scanf("%d", &choice);

            switch (choice) {
                case 1:
                    displayIngredientsAndSales();
                    break;
                case 2:
                    replenishIngredients();
                    break;
                case 3:
                    changeCoffeePrice();
                    break;
                case 0:
                    return;
                default:
                    printf("Invalid choice! Please choose again.\n");
            }
        }
    } else {
        printf("Incorrect password!\n");
    }
}

void checkIngredients() {
    if (coffee_beans <= LOW_THRESHOLD_BEANS) {
        printf("Alert: Low coffee beans!\n");
    }
    if (water <= LOW_THRESHOLD_WATER) {
        printf("Alert: Low water!\n");
    }
    if (milk <= LOW_THRESHOLD_MILK) {
        printf("Alert: Low milk!\n");
    }
    if (syrup <= LOW_THRESHOLD_SYRUP) {
        printf("Alert: Low chocolate syrup!\n");
    }
}

void displayIngredientsAndSales() {
    printf("\n---- Ingredients and Sales ----\n");
    printf("Coffee Beans: %.2f grams\n", coffee_beans);
    printf("Water: %.2f ml\n", water);
    printf("Milk: %.2f ml\n", milk);
    printf("Chocolate Syrup: %.2f ml\n", syrup);
    printf("Total Sales: AED %.2f\n", total_amount);

    char reset;
    printf("Would you like to reset total sales to zero? (y/n): ");
    scanf(" %c", &reset);
    if (reset == 'y') {
        total_amount = 0;
        printf("Total sales reset to zero. Please collect the money from the machine.\n");
    }
}

void replenishIngredients() {
    srand(time(0));
    coffee_beans = (rand() % 100) + 100;
    water = (rand() % 300) + 300;
    milk = (rand() % 200) + 200;
    syrup = (rand() % 100) + 100;

    printf("Ingredients have been replenished.\n");
    displayIngredientsAndSales();
}

void changeCoffeePrice() {
    printf("Enter new Espresso price: ");
    scanf("%f", &espresso_price);
    printf("Enter new Cappuccino price: ");
    scanf("%f", &cappuccino_price);
    printf("Enter new Mocha price: ");
    scanf("%f", &mocha_price);

    printf("Prices updated successfully.\n");
}
