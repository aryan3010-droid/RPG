#include <iostream>
#include <string>
#include <vector>
#include <cstdlib>
#include <ctime>

using namespace std;

class Enemy;

class Player {
public:
    string name;
    int health;
    int maxHealth;
    int attackPower;
    int level;
    int experience;
    int potions;

    Player(string n, int h, int a)
        : name(n), health(h), maxHealth(h), attackPower(a), level(1), experience(0), potions(1) {}

    void attack(Enemy& enemy);
    void takeDamage(int damage) {
        health -= damage;
        cout << name << " takes " << damage << " damage! Remaining HP: " << health << endl;
    }

    bool isAlive() {
        return health > 0;
    }

    void usePotion() {
        if (potions > 0) {
            int healAmount = 30;
            health = min(health + healAmount, maxHealth);
            potions--;
            cout << name << " uses a potion and restores " << healAmount << " HP! (Potions left: " << potions << ")" << endl;
        } else {
            cout << "No potions left!" << endl;
        }
    }

    void gainExperience(int xp) {
        experience += xp;
        cout << name << " gains " << xp << " XP!" << endl;
        if (experience >= level * 20) {
            level++;
            attackPower += 5;
            maxHealth += 20;
            health = maxHealth;
            experience = 0;
            cout << "Level up! Now Level " << level << ". Stats increased!" << endl;
        }
    }

    void displayStats() {
        cout << "\nPlayer Stats:\n";
        cout << "Name: " << name << "\nLevel: " << level << "\nHealth: " << health << "/" << maxHealth
             << "\nAttack Power: " << attackPower << "\nPotions: " << potions << "\nExperience: " << experience << "\n";
    }
};

class Enemy {
public:
    string name;
    int health;
    int attackPower;
    int xpReward;

    Enemy(string n, int h, int a, int xp) : name(n), health(h), attackPower(a), xpReward(xp) {}

    void attack(Player& player) {
        cout << name << " attacks " << player.name << " for " << attackPower << " damage!" << endl;
        player.takeDamage(attackPower);
    }

    void takeDamage(int damage) {
        health -= damage;
        cout << name << " takes " << damage << " damage! Remaining HP: " << health << endl;
    }

    bool isAlive() {
        return health > 0;
    }
};

void Player::attack(Enemy& enemy) {
    cout << name << " attacks " << enemy.name << " for " << attackPower << " damage!" << endl;
    enemy.takeDamage(attackPower);
}

Enemy generateRandomEnemy() {
    vector<Enemy> enemies = {
        Enemy("Goblin", 50, 5, 10),
        Enemy("Orc", 70, 8, 15),
        Enemy("Dark Wolf", 60, 6, 12),
        Enemy("Skeleton", 55, 7, 14)
    };
    int index = rand() % enemies.size();
    return enemies[index];
}

void startGame(Player& player) {
    Enemy enemy = generateRandomEnemy();
    cout << "\nYou enter a dark forest and encounter a " << enemy.name << "!" << endl;

    while (player.isAlive() && enemy.isAlive()) {
        cout << "\n1. Attack\n2. Use Potion\n3. Run\nChoose an action: ";
        int action;
        cin >> action;

        if (action == 1) {
            player.attack(enemy);
            if (enemy.isAlive()) {
                enemy.attack(player);
            }
        } else if (action == 2) {
            player.usePotion();
            if (enemy.isAlive()) {
                enemy.attack(player);
            }
        } else if (action == 3) {
            cout << "You escaped safely!" << endl;
            return;
        } else {
            cout << "Invalid action." << endl;
        }
    }

    if (!player.isAlive()) {
        cout << "You were defeated..." << endl;
    } else if (!enemy.isAlive()) {
        cout << "You defeated the " << enemy.name << "!" << endl;
        player.gainExperience(enemy.xpReward);
        player.potions += 1;
        cout << "You found a potion on the enemy's body!" << endl;
    }
}

int main() {
    srand(static_cast<unsigned int>(time(0)));
    Player player("Hero", 100, 10);

    while (true) {
        cout << "\n=== RPG Game ===\n";
        cout << "1. Start Game\n2. View Stats\n3. Exit\nChoose an option: ";
        int choice;
        cin >> choice;

        switch (choice) {
            case 1:
                startGame(player);
                break;
            case 2:
                player.displayStats();
                break;
            case 3:
                cout << "Goodbye!" << endl;
                return 0;
            default:
                cout << "Invalid option, try again." << endl;
        }
    }

    return 0;
}
