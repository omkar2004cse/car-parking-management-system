# car-parking-management-system
<br>
I am omkar jadhav
<br>
It is my frist project in Engineering second year by using c++ and oop concept.
<br>
#include<iostream>
#include<vector>
#include<windows.h>
#include<fstream>
using namespace std;

struct time
{
    int hh;
    int mm;
    int ss;
    char col1;
    char col2;
};

struct date
{
    int day;
    int month;
    int year;
    char sym1;
    char sym2;
};

class vehicle
{
    string pltno;
    int type;
    date dt;
    time arrive;
    time departure;

public:
    void addVehicle();
    void deleteVehicle();
    void printvehicle(vehicle v);
    void show();
    void displayTokens(); // Added function
    void showTotalBill(); // Added function
};

vector<vehicle> veh(100);
int static totalvehicle=0,totalcar=0,totalbike=0,totalamt=0,i=0;
fstream file;

void vehicle::addVehicle()
{
    vehicle *v = new vehicle;
    cout<<"Enter vehicle type(1 for Car/2 for Bike) : ";
    cin>>v->type;
    cout<<"Enter vehicle number : ";
    cin>>v->pltno;
    cout<<"Enter arrival time in hours minutes and seconds : ";
    cin>>v->arrive.hh>>v->arrive.col1>>v->arrive.mm>>v->arrive.col2>>v->arrive.ss;
    cout<<"Enter date in day month and year: ";
    cin>>v->dt.day>>v->dt.sym1>>v->dt.month>>v->dt.sym2>>v->dt.year;

    veh.at(i).pltno=v->pltno;
    veh.at(i).type=v->type;
    veh.at(i).arrive.hh=v->arrive.hh;
    veh.at(i).arrive.mm=v->arrive.mm;
    veh.at(i).arrive.ss=v->arrive.ss;
    veh.at(i).dt.day=v->dt.day;
    veh.at(i).dt.month=v->dt.month;
    veh.at(i).dt.year=v->dt.year;

    i++;
    totalvehicle++;

    if(v->type == 1)
    {
        totalcar++;
    }
    else
    {
        totalbike++;
    }

    cout<<"\nVehicle added successfully"<<endl;
}

int computeTimeDifference(time t1,time t2)
{
    int sec1, sec2, totalsec;
    time t3;
    //calculate difference
    //get time in total seconds
    sec1 = t1.hh*60*60 + t1.mm*60 + t1.ss;
    sec2 = t2.hh*60*60 + t2.mm*60 + t2.ss;

    totalsec = sec2-sec1;

    //extract time in Hours, Minutes and Seconds
    t3.mm = totalsec/60;
    t3.hh = t3.mm/60;
    t3.mm = t3.mm%60;
    t3.ss = totalsec%60;

    // cout<<"Time difference:" <<hh<<":"<<mm<<":"<<ss<<endl;
    return t3.hh;
}

void vehicle::deleteVehicle()
{
    string pno;
    int typ;
    time depart;
    int time_diff;
    int charge = 0;

    cout<<"Enter vehicle type (1 for Car/2 for Bike): ";
    cin>>typ;
    cout<<"Enter vehicle number: ";
    cin>>pno;
    cout<<"Enter departure time in hours minutes and seconds: ";
    cin>>depart.hh>>depart.col1>>depart.mm>>depart.col2>>depart.ss;

    for(int j = 0; j < i; j++)
    {
        if((veh.at(j).pltno == pno) && (veh.at(j).type == typ))
        {
            veh.at(j).departure.hh = depart.hh;
            veh.at(j).departure.mm = depart.mm;
            veh.at(j).departure.ss = depart.ss;

            time_diff = computeTimeDifference(veh.at(j).arrive, depart);

            if(veh.at(j).type == 1)
            {
                totalcar--;
                if(time_diff < 2)
                    charge = 20;
                else if((time_diff >= 2) && (time_diff < 5))
                    charge = 40;
                else
                    charge = 50;
            }
            else
            {
                totalbike--;
                if(time_diff < 2)
                    charge = 5;
                else if((time_diff >= 2) && (time_diff < 5))
                    charge = 10;
                else
                    charge = 20;
            }

            cout<<"\nVehicle with vehicle number "<<veh.at(j).pltno<<" has to pay Rs. "<<charge<<endl;

            // Update total amount
            totalamt += charge;

            // Write details to file
            file.open("parkingDatabase.txt", ios::app);
            if(!file)
            {
                cerr << "Error: file could not be opened" << endl;
                exit(1);
            }
            file << veh.at(j).type << "\t\t\t" << veh.at(j).pltno << "\t\t\t" << veh.at(j).dt.day << "/" << veh.at(j).dt.month << "/" << veh.at(j).dt.year << "\t\t\t" << veh.at(j).arrive.hh << ":" << veh.at(j).arrive.mm << ":" << veh.at(j).arrive.ss << "\t\t\t" << veh.at(j).departure.hh << ":" << veh.at(j).departure.mm << ":" << veh.at(j).departure.ss << endl;
            file.close();

            // Remove vehicle from vector
            veh.erase(veh.begin() + j);
            i--;
            totalvehicle--;

            break;
        }
    }
}    
void vehicle::printvehicle(vehicle v)
{
    cout<<v.type<<"\t\t\t"<<v.pltno<<"\t\t\t"<<v.dt.day<<"/"<<v.dt.month<<"/"<<v.dt.year<<"\t\t"<< v.arrive.hh<<":"<<v.arrive.mm<<":"<<v.arrive.ss<<endl;
}

void vehicle::show()
{
    cout<<"Vehicle Type\t\tVehicle Number\t\tDate\t\tArrival Time"<<endl;
    for(int j=0;j<i;j++)
    {
        printvehicle(veh[j]);
    }
}

void vehicle::displayTokens()
{
    cout << "Tokens:\n";
    for(int j = 0; j < i; j++)
    {
        cout << "Token: " << veh[j].pltno << endl;
        cout << "Arrival Time: " << veh[j].arrive.hh << ":" << veh[j].arrive.mm << ":" << veh[j].arrive.ss << endl;
        cout << "Date: " << veh[j].dt.day << "/" << veh[j].dt.month << "/" << veh[j].dt.year << endl;
        cout << "------------------------------------" << endl;
    }
    
}

void vehicle::showTotalBill()
{
    int totalBill = 0;
    for(int j = 0; j < i; j++)
    {
        int parkingDuration = computeTimeDifference(veh[j].arrive, veh[j].departure);
        int vehicleType = veh[j].type;
        int charge = 0;

        if(vehicleType == 1) // Car
        {
            if(parkingDuration < 2)
                charge = 20;
            else if(parkingDuration < 5)
                charge = 40;
            else
                charge = 50;
        }
        else // Bike
        {
            if(parkingDuration < 2)
                charge = 5;
            else if(parkingDuration < 5)
                charge = 10;
            else
                charge = 20;
        }

        totalBill += charge;
    }

   
}

void totalveh()
{
    cout<<"Total number of vehicle parked : "<< totalvehicle<<endl;
    cout<<"Total number of car parked : "<< totalcar<<endl;
    cout<<"Total number of bike parked : "<<  totalbike<<endl;
}

void totalamount()
{
    cout<<"Total Collection till now : "<<totalamt<<endl;
}

int main()
{
    int choice;
    char ans;
    system ( "cls" ) ;
    do
    {
        system ( "cls" ) ;
        cout<<""<<endl;
        cout<<"                 VEHICLE PARKING RESERVATION SYSTEM                 "<<endl;
        cout<<"1. Arrival of a vehicle"<<endl
            <<"2. Total number of vehicles parked"<<endl
            <<"3. Total Amount collected "<<endl
            <<"4. Display "<<endl
            <<"5. Show Tokens"<<endl
            <<"6. Show Total Bill"<<endl
            <<"7. Exit"<<endl;
        cout<<""<<endl
            <<"Enter your Choice : ";
        cin>>choice;

        switch(choice)
        {
            case 1: system ( "cls" ) ;
                    cout<<"Add : "<<endl;
                    veh.at(i).addVehicle();
                    break;

            case 2: system ( "cls" ) ;
                    totalveh();
                    break;

            case 3: system ( "cls" ) ;
                    totalamount();
                    break;
            case 4: system("cls");
                    veh.at(i).show();
                    break;
            case 5: system("cls");
                    veh.at(i).displayTokens();
                    break;
            case 6: system("cls");
            		veh.at(i).deleteVehicle();
            		veh.at(i).showTotalBill();
					 break;
            case 7: exit(0);
        }
        cout<<"\nDo you want to continue, press y/n : "<<endl;
        cin>>ans;
        if(ans=='n')
        {
            break;
        }
        else
        {
            continue;
        }
    }while(1);
    return 0;
}
