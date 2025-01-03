# try
#include <iostream>
#include <iomanip>
#include <fstream>
#include <string>
using namespace std;
#define MONTH 12
#define MAXPROD 5

int getMontlySales(string[], float[][MONTH]);
void displayMonthlySales (int, string[], float[][MONTH]);
void calAverageMonthlySales (int,  float [][MONTH], float []);
void findBestWorstMonth (int, float [][MONTH], int [][2]);
void sortDescending (int PNum, float avgMS[], int ranking[]);
void displayTableA (int PNum, string PName[], float avgMS[], int ranking[], int bestWorstMonth[][2]);
void findMonthlyRanking (int PNum, float sales[][MONTH], int ranking[][MAXPROD]);
void displayTableB(int PNum, string PName[], float sales[][MONTH], int rankings[][MAXPROD]);

string month[MONTH] = {"Jan", "Feb", "Mar", "Apr", "May", "Jun",    
                       "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"};

int main()
{
    ifstream inFile;
    int productNum, bestWorstMonth[MAXPROD][2], averageRanking[MAXPROD], monthlyRankings[MONTH][MAXPROD];
    float monthlySales[MAXPROD][MONTH]={0}, averageMonthlySales[MONTH]={0};
    string productName[MAXPROD];
                           
    productNum = getMontlySales (productName, monthlySales);
    //displayMonthlySales(productNum, productName, monthlySales);
    calAverageMonthlySales (productNum, monthlySales, averageMonthlySales);
    findBestWorstMonth (productNum, monthlySales, bestWorstMonth);
    sortDescending (productNum, averageMonthlySales, averageRanking);
    cout<<"\nTable A\n"<<endl;
    displayTableA (productNum, productName, averageMonthlySales, averageRanking, bestWorstMonth);
    findMonthlyRanking(productNum, monthlySales, monthlyRankings);
    cout<<"\nTable B\n"<<endl;
    displayTableB(productNum, productName, monthlySales, monthlyRankings);
    
    return 0;
}

int getMontlySales(string name[], float sales[][MONTH]) {
    ifstream inFile;
    string filename;
    int prodNum;
   
    do {
        cout<<"Enter filename: ";
        cin>>filename;
        inFile.open(filename);
        if (!inFile.is_open())
            cout<<"The file cannot be opened. Please re-enter\n";
    } while (!inFile.is_open());
        
    do {
        cout<<"Enter number of product (1 - 5): ";
        cin>>prodNum;
        if (prodNum < 1 || prodNum > 5)
            cout<<"Invalid input! It must be between 1 to 5\n";
            
    } while (prodNum < 1 || prodNum > 5);
    
    
    for (int n=0; n<prodNum; n++) {
        getline(inFile, name[n]);
        
        //to remove the newline and carriage character at the end of the string
        int len = name[n].length();
        for (int i=0; i<len; i++) {
            if (name[n][i]=='\r' || name[n][i]=='\n') {
                name[n][i]= '\0';
            }
        }

        for (int m=0; m<MONTH; m++) {
            inFile >> sales[n][m];
        }
        inFile.ignore();
        
    }
    inFile.close();
    return prodNum;
}

void displayMonthlySales (int pNum, string pName[], float pMSales[][MONTH]) {
    cout<<fixed<<setprecision(2);
    for (int n=0; n<pNum; n++) {
        cout<<pName[n]<<endl;
        for (int m=0; m<MONTH; m++) {
            cout<<month[m]<<" - $ "<<setw(10)<<pMSales[n][m]<<endl;
        }
    }
}

// Function to calculate the average monthly sales for each product
void calAverageMonthlySales (int PNum,  float MSales[][MONTH], float avgMS[]) {
    float total;
    for (int n=0; n<PNum; n++){
        total = 0;
        for (int m=0; m<12; m++){
            total += MSales[n][m];
        }
        avgMS[n] = total/12;
    }
}

void findBestWorstMonth (int PNum, float MSales[][MONTH], int bestWorstMonth[][2]) {
    float bestValue, worstValue;
    int bestIndex, worstIndex;
    
    for (int n=0; n<PNum; n++){
        
        bestValue = MSales[n][0];
        bestIndex = 0;
        worstValue = MSales[n][0];
        worstIndex = 0;
        
        for (int m=0; m<12; m++){
            //find best MONTH
            if (bestValue < MSales[n][m]){
                bestValue = MSales[n][m];
                bestIndex = m;
            }
            //find worst MONTH
            if (worstValue > MSales[n][m]){
                worstValue = MSales[n][m];
                worstIndex = m;
            }
        }
        bestWorstMonth[n][0] = bestIndex;
        bestWorstMonth[n][1] = worstIndex;
    }
}

void sortDescending (int PNum, float avgMS[], int ranking[]) {
    for (int n=0; n<PNum; n++)
        ranking[n]=n;
        
     for (int step=0; step<PNum - 1; step++) {
        for (int i=0; i<PNum - step - 1; i++)
        {
            if (avgMS[ranking[i]] < avgMS[ranking[i+1]]) {
                int temp;
                
                temp = ranking[i];
                ranking[i] = ranking[i+1];
                ranking[i+1] = temp;
            } 
        }
     }
}

void displayTableA (int PNum, string PName[], float avgMS[], int ranking[], int bestWorstMonth[][2]) {
    cout << left << setw(15) << "Product Name"
         << setw(24) << "Average Monthly Sales"
         << setw(20) << "Best Selling Month"
         << setw(18) << "Worst Selling Month" << endl;
         
    for (int n=0; n<PNum; n++) {
        cout << left << setw(15) << PName[ranking[n]]
             << "$" << setw(23) << fixed << setprecision(2) << avgMS[ranking[n]]
             << setw(20) << month[bestWorstMonth[ranking[n]][0]]
             << setw(38) << month[bestWorstMonth[ranking[n]][1]]
             << endl;
    }
}

void findMonthlyRanking(int PNum, float sales[][MONTH], int ranking[][MAXPROD]) {
    for (int m = 0; m < MONTH; m++) {
        for (int n = 0; n < PNum; n++) {
            ranking[m][n] = n;
        }

        // Sort rankings for the current month in descending order
        for (int i = 0; i < PNum - 1; i++) {
            for (int step = i + 1; step < PNum; step++) {
                if (sales[ranking[m][i]][m] < sales[ranking[m][step]][m]) {
                    // Swap the rankings
                    int temp = ranking[m][i];
                    ranking[m][i] = ranking[m][step];
                    ranking[m][step] = temp;
                }
            }
        }
    }
}

void displayTableB(int PNum, string PName[], float sales[][MONTH], int rankings[][MAXPROD]) {
    // Print the table header
    cout << left << setw(10) << "Month";
    for (int rank = 1; rank <= PNum; rank++) {
        cout << setw(18) << ("Rank #" + to_string(rank));
    }
    cout << endl;
    cout << endl;

    // Loop through each month to display data
    for (int m = 0; m < MONTH; m++) {
        // Print the month name
        cout << left << setw(10) << month[m];

        // Print product names for the month
        for (int rank = 0; rank < PNum; rank++) {
            int productIndex = rankings[m][rank];
            cout << setw(18) << PName[productIndex];
        }
        cout << endl;
        

        // Print sales values under the product names
        cout << left << setw(10) << ""; // Indentation for sales
        for (int rank = 0; rank < PNum; rank++) {
            int productIndex = rankings[m][rank];
            // Correct formatting to show sales with two decimal places
            cout << fixed << setprecision(2);
            // Use a more consistent width for sales display
            cout << "$" << setw(16) << sales[productIndex][m] << " ";  // Reduce the space to make $ closer to the value
        }
        cout << endl;
        cout << endl;
    }
}








