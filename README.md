# ADS-assignment
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_REG_LENGTH 10
#define MAX_MAKE_MODEL_LENGTH 50
#define MAX_COLOR_LENGTH 20

struct Car {
    char reg_number[MAX_REG_LENGTH];
    char make_model[MAX_MAKE_MODEL_LENGTH];
    char color[MAX_COLOR_LENGTH];
    int num_owners;
    int reserved;
    float reserve_amount;
    struct Car* next;
};

struct Car* createCar(char reg[], char model[], char clr[], int owners, int res, float res_amt) {
    struct Car* car = (struct Car*)malloc(sizeof(struct Car));
    if (car == NULL) {
        printf("Memory allocation failed.\n");
        exit(EXIT_FAILURE);
    }
    strcpy(car->reg_number, reg);
    strcpy(car->make_model, model);
    strcpy(car->color, clr);
    car->num_owners = owners;
    car->reserved = res;
    car->reserve_amount = res_amt;
    car->next = NULL;
    return car;
}

void addCar(struct Car** showroom, struct Car* car) {
    if (*showroom == NULL)
        *showroom = car;
    else {
        struct Car* current = *showroom;
        while (current->next != NULL)
            current = current->next;
        current->next = car;
    }
}

void sellCar(struct Car** showroom, char reg[]) {
    if (*showroom == NULL) {
        printf("Showroom is empty.\n");
        return;
    }
    if (strcmp((*showroom)->reg_number, reg) == 0) {
        struct Car* temp = *showroom;
        *showroom = (*showroom)->next;
        free(temp);
        printf("Car with registration number %s sold successfully.\n", reg);
        return;
    }
    struct Car* prev = NULL;
    struct Car* current = *showroom;
    while (current != NULL && strcmp(current->reg_number, reg) != 0) {
        prev = current;
        current = current->next;
    }
    if (current != NULL) {
        prev->next = current->next;
        free(current);
        printf("Car with registration number %s sold successfully.\n", reg);
    } else {
        printf("Car with registration number %s not found.\n", reg);
    }
}

void reserveUnreserveCar(struct Car* showroom, char reg[], float reserve_amount) {
    struct Car* current = showroom;
    while (current != NULL) {
        if (strcmp(current->reg_number, reg) == 0) {
            current->reserved = !current->reserved;
            current->reserve_amount = reserve_amount;
            printf("Car with registration number %s %s successfully.\n", reg, (current->reserved ? "reserved" : "unreserved"));
            return;
        }
        current = current->next;
    }
    printf("Car with registration number %s not found.\n", reg);
}

void viewAllCars(struct Car* showroom) {
    if (showroom == NULL) {
        printf("Showroom is empty.\n");
    } else {
        printf("Cars in the showroom:\n");
        struct Car* current = showroom;
        while (current != NULL) {
            printf("Registration: %s, Make and Model: %s, Color: %s, Owners: %d, Reserved: %s, Reserve Amount: %.2f\n", current->reg_number, current->make_model, current->color, current->num_owners, (current->reserved ? "Yes" : "No"), current->reserve_amount);
            current = current->next;
        }
    }
}

void viewSpecificCar(struct Car* showroom, char reg[]) {
    struct Car* current = showroom;
    while (current != NULL) {
        if (strcmp(current->reg_number, reg) == 0) {
            printf("Registration: %s, Make and Model: %s, Color: %s, Owners: %d, Reserved: %s, Reserve Amount: %.2f\n", current->reg_number, current->make_model, current->color, current->num_owners, (current->reserved ? "Yes" : "No"), current->reserve_amount);
            return;
        }
        current = current->next;
    }
    printf("Car with registration number %s not found.\n", reg);
}

int main() {
    struct Car* showroom = NULL;

    while (1) {
        printf("\nProgram Menu:\n");
        printf("1. Add a new car to the showroom\n");
        printf("2. Sell a car from the showroom\n");
        printf("3. Reserve/Unreserve a car in the showroom\n");
        printf("4. View all cars in the showroom\n");
        printf("5. View a specific car in the showroom\n");
        printf("6. This should be an appropriate option that you provide\n");
        printf("7. Exit the system\n");

        printf("Enter your choice: ");
        int choice;
        scanf("%d", &choice);

        if (choice == 1) {
            char reg[MAX_REG_LENGTH], model[MAX_MAKE_MODEL_LENGTH], clr[MAX_COLOR_LENGTH];
            int owners;
            printf("Enter registration number: ");
            scanf("%s", reg);
            printf("Enter make and model: ");
            scanf(" %[^\n]", model);
            printf("Enter color: ");
            scanf("%s", clr);
            printf("Enter number of owners: ");
            scanf("%d", &owners);
            struct Car* car = createCar(reg, model, clr, owners, 0, 0);
            addCar(&showroom, car);
        } else if (choice == 2) {
            char reg[MAX_REG_LENGTH];
            printf("Enter registration number of the car to sell: ");
            scanf("%s", reg);
            sellCar(&showroom, reg);
        } else if (choice == 3) {
            char reg[MAX_REG_LENGTH];
            float reserve_amount;
            printf("Enter registration number of the car to reserve/unreserve: ");
            scanf("%s", reg);
            printf("Enter reserve amount (0 to unreserve): ");
            scanf("%f", &reserve_amount);
            reserveUnreserveCar(showroom, reg, reserve_amount);
        } else if (choice == 4) {
            viewAllCars(showroom);
        } else if (choice == 5) {
            char reg[MAX_REG_LENGTH];
            printf("Enter registration number of the car to view: ");
            scanf("%s", reg);
            viewSpecificCar(showroom, reg);
        } else if (choice == 6) {
            // Additional feature implementation goes here
        } else if (choice == 7) {
            printf("Exiting the system...\n");
            break;
        } else {
            printf("Invalid choice. Please enter a number between 1 and 7.\n");
        }
    }

    // Free allocated memory
    struct Car* current = showroom;
    struct Car* next;
    while (current != NULL) {
        next = current->next;
        free(current);
        current = next;
    }

    return 0;
}
