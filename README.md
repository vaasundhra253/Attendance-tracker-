# Attendance-tracker-
package com.company;
public class Main {
public static void main(String[] args) {
new Attendance();
}
}
package com.company;
import javax.swing.*;
import java.awt.*
import java.sql.*;
import java.util.*;
public class Attendance {
private JTextField nameData;
private JTextField totalClasses;
private JTable table1;
private JButton ADDRECORDButton;
private JButton UPDATERECORDButton;
private JPanel resultPanel;
private JComboBox subject;
private JTextField attendance;
private int totalMarks=0;
JFrame attendF = new JFrame();
public Attendance(){
attendF.setContentPane(resultPanel);
attendF.pack();
attendF.setLocationRelativeTo(null);
attendF.setVisible(true);
tableData();
ADDRECORDButton.addActionListener(new ActionListener() {
@Override
public void actionPerformed(ActionEvent e) {
if( nameData.getText().equals("")|| attendance.getText().equals("")){
JOptionPane.showMessageDialog(null,"Please Fill NAME and Total Classes Fields to add Record.");
}else{
try {
String sql = "insert into attendance"+"(NAME,SUBJECT,TOTAL_CLASSES,CLASSES_ATTENDED,TOTAL_ATTENDANCE)"+"values (?,?,?,?,?)";
Class.forName("com.mysql.cj.jdbc.Driver");
Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/intern","root","root");
PreparedStatement statement = connection.prepareStatement(sql);
double attend = (Double.parseDouble(attendance.getText())/Double.parseDouble(totalClasses.getText()))*100.0;
statement.setString(1,nameData.getText());
statement.setString(2,""+subject.getSelectedItem());
statement.setString(3,totalClasses.getText());
statement.setString(4,attendance.getText());
statement.setString(5,String.format("%.2f",attend)+"%");
statement.executeUpdate();
JOptionPane.showMessageDialog(null,"STUDENT ADDED SUCCESSFULLY");
attendance.setText("");
}catch (Exception ex){
JOptionPane.showMessageDialog(null,ex.getMessage());
}
tableData();
}
}
});
UPDATERECORDButton.addActionListener(new ActionListener() {
@Override
public void actionPerformed(ActionEvent e) {
String sql="";
double attend = (Double.parseDouble(attendance.getText())/Double.parseDouble(totalClasses.getText()))*100.0;
String attend1 = String.format("%.2f",attend)+"%";
try{
if(subject.getSelectedIndex()==0){
sql = "UPDATE attendance "+"SET CLASSES_ATTENDED= '"+attendance.getText()+"' ,TOTAL_ATTENDANCE= '"+attend1+"' "+" WHERE NAME= '"+nameData.getText()+"' AND SUBJECT='PHYSICS'";
}else if(subject.getSelectedIndex()==1){
sql = "UPDATE attendance "+"SET CLASSES_ATTENDED= '"+attendance.getText()+"' ,TOTAL_ATTENDANCE= '"+attend1+"' "+" WHERE NAME= '"+nameData.getText()+"' AND SUBJECT='CHEMISTRY'";
}else {
sql = "UPDATE attendance "+"SET CLASSES_ATTENDED= '"+attendance.getText()+"' ,TOTAL_ATTENDANCE= '"+attend1+"' "+" WHERE NAME= '"+nameData.getText()+"' AND SUBJECT='MATHS'";
}
Class.forName("com.mysql.cj.jdbc.Driver");
Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/intern","root","root");
PreparedStatement statement = connection.prepareStatement(sql);
statement.executeUpdate();
JOptionPane.showMessageDialog(null,"Attendance Updated successfully");
}catch (Exception e2){
System.out.println(e2);
}
tableData();
}
});
table1.addMouseListener(new MouseAdapter() {
@Override
public void mouseClicked(MouseEvent e) {
int i=1;
DefaultTableModel dm = (DefaultTableModel)table1.getModel();
int rowIndex = table1.getSelectedRow();
nameData.setText(dm.getValueAt(rowIndex,0).toString());
attendance.setText(dm.getValueAt(rowIndex,3).toString());
totalClasses.setText(dm.getValueAt(rowIndex,2).toString());
}
});
}
public void tableData() {
try{
String a= "Select* from attendance";
Class.forName("com.mysql.cj.jdbc.Driver");
Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/intern","root","root");
Statement resultQuery = connection.createStatement();
ResultSet queryResult = resultQuery.executeQuery(a);
table1.setModel(buildTableModel(queryResult));
}catch (Exception ex1){
JOptionPane.showMessageDialog(null,ex1.getMessage());
}
}
public static DefaultTableModel buildTableModel(ResultSet rs)
throws SQLException {
ResultSetMetaData metaData = rs.getMetaData();
// names of columns
Vector<String> columnNames = new Vector<String>();
int columnCount = metaData.getColumnCount();
for (int column = 1; column <= columnCount; column++) {
columnNames.add(metaData.getColumnName(column));
}
// data of the table
Vector<Vector<Object>> data = new Vector<Vector<Object>>();
while (rs.next()) {
Vector<Object> vector = new Vector<Object>();
for (int columnIndex = 1; columnIndex <= columnCount; columnIndex++) {
vector.add(rs.getObject(columnIndex));
}
data.add(vector);
}
return new DefaultTableModel(data, columnNames);
}
}
