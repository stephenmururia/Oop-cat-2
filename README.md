import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.sql.*;

public class RegistrationForm {
    private JFrame frame;
    private JTextField nameField, mobileField;
    private JRadioButton male, female;
    private JComboBox<String> day, month, year;
    private JTextArea addressField;
    private JCheckBox terms;
    private JTextArea displayArea;
    
    Connection conn;

    public RegistrationForm() {
        frame = new JFrame("Registration Form");
        frame.setSize(500, 400);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setLayout(new GridLayout(1, 2));

        JPanel formPanel = new JPanel(new GridLayout(7, 2));
        displayArea = new JTextArea();

        nameField = new JTextField();
        mobileField = new JTextField();
        male = new JRadioButton("Male");
        female = new JRadioButton("Female");
        ButtonGroup genderGroup = new ButtonGroup();
        genderGroup.add(male);
        genderGroup.add(female);
        
        String[] days = {"1", "2", "3", "4", "5"};
        String[] months = {"Jan", "Feb", "Mar", "Apr", "May"};
        String[] years = {"1990", "1991", "1992", "1993"};
        day = new JComboBox<>(days);
        month = new JComboBox<>(months);
        year = new JComboBox<>(years);

        addressField = new JTextArea();
        terms = new JCheckBox("Accept Terms And Conditions");
        JButton submit = new JButton("Submit");
        JButton reset = new JButton("Reset");
        
        submit.addActionListener(e -> saveData());
        reset.addActionListener(e -> resetForm());
        
        formPanel.add(new JLabel("Name"));
        formPanel.add(nameField);
        formPanel.add(new JLabel("Mobile"));
        formPanel.add(mobileField);
        formPanel.add(new JLabel("Gender"));
        JPanel genderPanel = new JPanel();
        genderPanel.add(male);
        genderPanel.add(female);
        formPanel.add(genderPanel);
        formPanel.add(new JLabel("DOB"));
        JPanel dobPanel = new JPanel();
        dobPanel.add(day);
        dobPanel.add(month);
        dobPanel.add(year);
        formPanel.add(dobPanel);
        formPanel.add(new JLabel("Address"));
        formPanel.add(new JScrollPane(addressField));
        formPanel.add(terms);
        formPanel.add(new JLabel(""));
        formPanel.add(submit);
        formPanel.add(reset);

        frame.add(formPanel);
        frame.add(new JScrollPane(displayArea));
        frame.setVisible(true);
        
        connectDB();
    }
    
    private void connectDB() {
        try {
            conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/registration_db", "root", "password");
        } catch (Exception e) {
            JOptionPane.showMessageDialog(frame, "Database Connection Failed!", "Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    private void saveData() {
        if (!terms.isSelected()) {
            JOptionPane.showMessageDialog(frame, "Please accept terms and conditions.");
            return;
        }
        try {
            String name = nameField.getText();
            String mobile = mobileField.getText();
            String gender = male.isSelected() ? "Male" : "Female";
            String dob = day.getSelectedItem() + "-" + month.getSelectedItem() + "-" + year.getSelectedItem();
            String address = addressField.getText();

            PreparedStatement pst = conn.prepareStatement("INSERT INTO users (name, mobile, gender, dob, address) VALUES (?, ?, ?, ?, ?)");
            pst.setString(1, name);
            pst.setString(2, mobile);
            pst.setString(3, gender);
            pst.setString(4, dob);
            pst.setString(5, address);
            pst.executeUpdate();

            displayArea.setText("Data Saved:\nName: " + name + "\nMobile: " + mobile + "\nGender: " + gender + "\nDOB: " + dob + "\nAddress: " + address);
        } catch (Exception e) {
            JOptionPane.showMessageDialog(frame, "Error saving data.", "Error", JOptionPane.ERROR_MESSAGE);
        }
    }
    
    private void resetForm() {
        nameField.setText("");
        mobileField.setText("");
        genderGroup.clearSelection();
        addressField.setText("");
        terms.setSelected(false);
        displayArea.setText("");
    }
    
    public static void main(String[] args) {
        new RegistrationForm();
    }
}
