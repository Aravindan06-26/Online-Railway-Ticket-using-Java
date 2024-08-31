import java.sql.*;
import java.util.Scanner;
import java.util.Random;

public class RailwayTicket{
    private static final int min=1000;
    private static final int max=9999;
    
    public static class user{
        
        private String username;
        private String password;

        Scanner sc=new Scanner(System.in);
        public user(){

        }
        
        public String getUsername(){
            System.out.println("\t\t\t\t\t\tWELCOME TO RAILWAYS");
            System.out.println("Enter username:");
            username=sc.nextLine();
            return username;
        }

        public String getPassword(){
            System.out.println("Enter Password:");
            password=sc.nextLine();
            return password;
        }
    }

    public static class PnrRecord{
        private int pnrNumber;
        private String passengerName;
        private String trainNumber;
        private String classType;
        private String journeyDate;
        private String from;
        private String to;

        Scanner sc=new Scanner(System.in);

        public int getpnrNumber(){
            Random random=new Random();
            pnrNumber=random.nextInt(max)+min;
            return pnrNumber;
        }

        public String getpassengerName(){
            System.out.println("Enter the Passenger name:");
            passengerName=sc.nextLine();
            return passengerName;
        }

        public String gettrainNumber(){
            System.out.println("Enter the Train number:");
            trainNumber=sc.nextLine();
            return trainNumber;
        }

        public String getclassType(){
            System.out.println("Enter the Class type:");
            classType=sc.nextLine();
            return classType;
        }

        public String getjourneyDate(){
            System.out.println("Enter the Journey date as 'yyyy-mm-dd' format:");
            journeyDate=sc.nextLine();
            return journeyDate;
        }

        public String getfrom(){
            System.out.println("Enter the Starting place:");
            from=sc.nextLine();
            return from;
        }

        public String getto(){
            System.out.println("Enter the Destination place:");
            to=sc.nextLine();
            return to;
        }
    }

    public static void main(String[] args) {
        Scanner sc=new Scanner(System.in);
        user u1=new user();
        String username=u1.getUsername();
        String password=u1.getPassword();

        String url="jdbc:mysql://localhost:3306/railway";
        try{
            Class.forName("com.mysql.cj.jdbc.Driver");

            try(Connection connection = DriverManager.getConnection(url, username, password)){
                System.out.println("User Connection Granted.\n");
                while(true){
                    String InsertQuery = "insert into reservations values (?, ?, ?, ?, ?, ?, ?)";
                    String DeleteQuery = "Delete FROM reservations WHERE pnr_number = ?";
                    String ShowQuery = "Select * from reservations";

                    System.out.println("Enter the choice:");
                    System.out.println("1.Insert Record\n2.Delete Record\n3.Show all records\n4.exit\n");
                    int choice=sc.nextInt();

                    if(choice == 1){

                        PnrRecord p1=new PnrRecord();
                        int pnr_number=p1.getpnrNumber();
                        String passengerName=p1.getpassengerName();
                        String trainNumber=p1.gettrainNumber();
                        String classType=p1.getclassType();
                        String journeyDate=p1.getjourneyDate();
                        String getfrom=p1.getfrom();
                        String getto=p1.getto();
                        try(PreparedStatement preparedStatement = connection.prepareStatement(InsertQuery)){
                            preparedStatement.setInt(1,pnr_number);
                            preparedStatement.setString(2,passengerName);
                            preparedStatement.setString(3,trainNumber);
                            preparedStatement.setString(4,classType);
                            preparedStatement.setString(5,journeyDate);
                            preparedStatement.setString(6,getfrom);
                            preparedStatement.setString(7,getto);

                            int rowsAffected=preparedStatement.executeUpdate();
                            if(rowsAffected>0){
                                System.out.println("Record added successfully");
                            }

                            else{
                                System.out.println("No records were added");
                            }
                        }

                        catch (SQLException e){
                            System.out.println("SQLException:"+e.getMessage());
                        }
                    }

                    else if(choice == 2){
                        System.out.println("Enter the PNR number to delete the record");
                        int pnrNumber=sc.nextInt();
                        try(PreparedStatement preparedStatement = connection.prepareStatement(DeleteQuery)){
                        preparedStatement.setInt(1,pnrNumber);
                        int rowsAffected=preparedStatement.executeUpdate();
                            if(rowsAffected>0){
                                System.out.println("Record deleted successfully");
                            }

                            else{
                                System.out.println("No records were deletd");
                            }  
                    }
                    catch (SQLException e){
                        System.out.println("SQLException:"+e.getMessage());
                    }

                }

                else if(choice == 3){
                    try(PreparedStatement preparedStatement = connection.prepareStatement(ShowQuery);
                        ResultSet resultSet= preparedStatement.executeQuery()){
                        System.out.println("\nAll records printing");
                        while(resultSet.next()){
                            String pnrNumber=resultSet.getString("pnr_number");
                            String passengerName=resultSet.getString("passenger_name");
                            String trainNumber=resultSet.getString("train_number");
                            String classType=resultSet.getString("class_type");
                            String journeyDate=resultSet.getString("journey_date");
                            String fromLocation=resultSet.getString("from_location");
                            String toLocation=resultSet.getString("to_location");

                            System.out.println("PNR Number :"+pnrNumber);
                            System.out.println("Passenger Name :"+passengerName);
                            System.out.println("Train Number :"+trainNumber);
                            System.out.println("Class Type :"+classType);
                            System.out.println("Journey Date :"+journeyDate);
                            System.out.println("From Location :"+fromLocation);
                            System.out.println("To Location :"+toLocation);
                            System.out.println("--------------------------");
                        }
                    }
                    catch(SQLException e){
                        System.out.println("SQLException:"+e.getMessage());
                    }
            }

            else if(choice == 4){
                System.out.println("Exiting the program");
                break;
            }
            else{
                System.out.println("Invalid Choice Entered");
            }
        }

    }
    catch (SQLException e){
        System.out.println("SQLException:"+e.getMessage());
    }
    }
    catch(ClassNotFoundException e){
        System.out.println("Error loading JDBC griver:"+e.getMessage());
    }

    sc.close();
}
}


