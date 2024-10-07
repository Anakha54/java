import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.MouseEvent;
import java.awt.event.MouseAdapter;
import javax.swing.border.*;
import java.io.*;
import java.io.*;
import java.util.*;

class Twopass{
    private static JTextArea textArea;
    private static File inputFile = new File("input.txt");
    private static File optabFile = new File("optab.txt");
    private static File symtabFile = new File("symtab.txt");
    private static File intermediateFile = new File("intermediate.txt");
    private static File objcodw = new File("object_code.txt");
    private static String op="ADD-18\nAND-40\nCOMP-28\nDIV-24\nJ-3C\nJEQ-30\nJGT-34\nJLT-38\nJSUB-48\nLDA-00\nLDCH-50\nLDL-08\nLDX-0\nMUL-20\nOR-44\nRD-D8\nRSUB-4C\nSTA-0C\nSTCH-54\nSTL-14\nSTSW-E8\nSTX-10\nSUB-1C\nTD-E0\nTIX-2C\nWD-DC\nEND-*";
    public static void main(String[] args) {
        SwingUtilities.invokeLater(Twopass::new);
    }

    public Twopass() {
        JFrame frame = new JFrame("Two Pass Assembler");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(1300, 750);
        frame.setLayout(null);

        frame.getContentPane().setBackground(new Color(195,195,195));

        JLabel inLabel = new JLabel("Input");
        inLabel.setBounds(140, 05, 250, 50);
        inLabel.setForeground(new Color(0,0,0));
        inLabel.setFont(new Font("Baskerville Old Face", Font.BOLD, 20));
        frame.add(inLabel);
        JTextArea inputt = new JTextArea();
        inputt.setBorder(BorderFactory.createLineBorder(Color.BLACK, 3));
        inputt.setBackground(new Color(224,255,255));
        inputt.setForeground(new Color(0,0,0));
        inputt.setFont(new Font("Baskerville Old Face", Font.PLAIN, 16));
        JScrollPane inscroll = new JScrollPane(inputt);
        inscroll.setBounds(30, 50, 350, 450);
        frame.add(inscroll);

        JLabel outLabel = new JLabel("Object Code");
        outLabel.setBounds(830, 05, 250, 50);
        outLabel.setForeground(new Color(0,0,0));
        outLabel.setFont(new Font("Baskerville Old Face", Font.BOLD, 20));
        frame.add(outLabel);
        JTextArea objectt = new JTextArea();
        objectt.setBorder(BorderFactory.createLineBorder(Color.BLACK, 3));
        objectt.setBounds(700, 50, 550, 250);
        objectt.setBackground(new Color(224,255,255));
        objectt.setForeground(new Color(0,0,0));
        objectt.setFont(new Font("Baskerville Old Face", Font.PLAIN, 16));
        objectt.setEditable(false);
        frame.add(objectt);

        JTextArea codeArea = new JTextArea();
        codeArea.setBorder(BorderFactory.createLineBorder(Color.BLACK, 3));
        codeArea.setBackground(new Color(224,255,255));
        codeArea.setForeground(new Color(0,0,0));
        codeArea.setFont(new Font("Baskerville Old Face", Font.PLAIN, 16));
        JScrollPane codescroll = new JScrollPane(codeArea);
        codescroll.setBounds(700, 350, 550, 280);
        frame.add(codescroll);
        codeArea.setEditable(false);
        frame.add(codescroll);



        JButton asButton = new JButton("Assemble");
        asButton.setBorder(BorderFactory.createLineBorder(Color.BLACK, 3));
        asButton.setBounds(30, 530, 350, 40);
        asButton.setBackground(new Color(231,131,177));
        asButton.setForeground(new Color(0, 0, 0));
        frame.add(asButton);


        JButton clearButton = new JButton("ReAssemble");
        clearButton.setBorder(BorderFactory.createLineBorder(Color.BLACK, 3));
        clearButton.setBounds(30, 590,350, 40);
        clearButton.setBackground(new Color(221,231,182));
        clearButton.setForeground(new Color(0,0, 0));
        frame.add(clearButton);

        JLabel label = new JLabel("Select an Option:");
        label.setBounds(400, 245, 250, 50);
        label.setForeground(new Color(0,0,0));
        label.setFont(new Font("Baskerville Old Face", Font.BOLD, 20));
        frame.add(label);

        String[] items = { "Intermediate Code", "SymTab", "OpTab" };
        JComboBox<String> comboBox = new JComboBox<>(items);
        comboBox.setBounds(400, 300, 250, 50);
        comboBox.setFont(new Font("Baskerville Old Face", Font.PLAIN, 14));
        comboBox.setBackground(new Color(231,131,177));
        comboBox.setForeground(new Color(0,0,0));
        comboBox.setBorder(BorderFactory.createLineBorder(Color.DARK_GRAY, 2));
        Border dBorder = comboBox.getBorder();
        Color dBackground = comboBox.getBackground();
        comboBox.addMouseListener(new MouseAdapter() {
            @Override
            public void mouseEntered(MouseEvent e) {
                // Change border color when mouse hovers
                comboBox.setBorder(BorderFactory.createLineBorder(Color.BLUE, 2));
                comboBox.setBackground(new Color( 176,224, 230));
            }

            @Override
            public void mouseExited(MouseEvent e) {
                // Reset to the original border when the mouse exits
                comboBox.setBorder(dBorder);
                comboBox.setBackground(dBackground);
            }
        });
        frame.add(comboBox);

        addee(asButton);
        addee(clearButton);
        
         asButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                

                try (BufferedWriter inWriter = new BufferedWriter(new FileWriter(inputFile))) {
                    String inputCode = inputt.getText();
                    if(inputCode==null){
                    objectt.setText(" ");
                    }
                    inWriter.write(inputCode);
                } catch (IOException ex) {
                    ex.printStackTrace();
                }
                try (BufferedWriter opcodeWriter = new BufferedWriter(new FileWriter(optabFile))) {
                     opcodeWriter.write(op+" ");
                 } catch (IOException ex) {
                    ex.printStackTrace();
                }
                try{
                String label, opcode, operand, code, mnemonic;
                label = opcode = operand = code = mnemonic = "";
                int programLength = passOne(label, opcode, operand, code, mnemonic);
                passTwo(label, opcode, operand, code, mnemonic, programLength);
                } catch (IOException d){
                    d.printStackTrace();
                }
                try (BufferedReader reader = new BufferedReader(new FileReader(objcodw))) {
                    StringBuilder fileContent = new StringBuilder();
                    String objcode;

                    while ((objcode = reader.readLine()) != null) {
                        fileContent.append(objcode).append(System.lineSeparator());
                    }

                    objectt.setText(fileContent.toString());
                } catch (IOException f) {
                    f.printStackTrace();
                }

            }
        });

               comboBox.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                String selectedItem = (String) comboBox.getSelectedItem();
                codeArea.setText("");

                if ("Intermediate Code".equals(selectedItem)) {
                    try (BufferedReader reader = new BufferedReader(new FileReader(intermediateFile))) {
                        StringBuilder content = new StringBuilder();
                        String line;
                        while ((line = reader.readLine()) != null) {
                            content.append(line).append("\n");
                        }
                        codeArea.setText(content.toString());
                    } catch (IOException ex) {
                        codeArea.setText("Error reading file: " + ex.getMessage());
                    }
                } else if ("SymTab".equals(selectedItem)) {
                    try (BufferedReader reader = new BufferedReader(new FileReader(symtabFile))) {
                        StringBuilder content = new StringBuilder();
                        String line;
                        while ((line = reader.readLine()) != null) {
                            content.append(line).append("\n");
                        }
                        codeArea.setText(content.toString());
                    } catch (IOException ex) {
                        codeArea.setText("Error reading file: " + ex.getMessage());
                    }
                } else if ("OpTab".equals(selectedItem)) {
                    codeArea.setText(op);
                }
            }
        });


        clearButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                try{
                    String inCode ;
                    String inCode1;
                    if((inCode = inputt.getText())!=null||(inCode1 = objectt.getText())!=null){
                        inputt.setText("");
                        objectt.setText("");
                        codeArea.setText("");
                    }
                }
                catch(NullPointerException g){
                    g.printStackTrace();
                }

            }
        });

        frame.setVisible(true);
    }
    public static void addee(JButton button) {
        Color originalColor = button.getBackground(); 

        // Add a mouse listener for hover effect
        button.addMouseListener(new MouseAdapter() {
            @Override
            public void mouseEntered(MouseEvent e) {
                button.setBackground(new Color( 176,224, 230)); 
                button.setBorder(BorderFactory.createLineBorder(new Color(179, 177, 137)));
            }

            @Override
            public void mouseExited(MouseEvent e) {
                button.setBackground(originalColor); 
                button.setBorder(BorderFactory.createEmptyBorder());
            }
        });


    }

   
    public static int passOne(String label, String opcode, String operand, String code, String mnemonic) throws IOException {
    int locctr = 0, start = 0, length;
    boolean found;
    try{
        Scanner inScanner = new Scanner(inputFile);
        BufferedWriter symtabWriter = new BufferedWriter(new FileWriter(symtabFile, false));
        BufferedWriter inwriter = new BufferedWriter(new FileWriter(intermediateFile, false));

        label = inScanner.next();
        opcode = inScanner.next();
        operand = inScanner.next();

        if (opcode.equals("START")) {
            start = Integer.parseInt(operand, 16); 
            locctr = start;
            inwriter.write(label + "\t" + opcode + "\t" + operand + "\n");

            if (inScanner.hasNext()) {
                label = inScanner.next();
                opcode = inScanner.next();
                operand = inScanner.next();
            }
        }
        while (!opcode.equals("END")) {
            inwriter.write(Integer.toHexString(locctr).toUpperCase() + "\t" + label + "\t" + opcode + "\t" + operand + "\n");

            if (!label.equals("**")) {
                symtabWriter.write(label + "\t" + Integer.toHexString(locctr).toUpperCase() + "\n");
            }

            found = false;

            try (Scanner optabScanner = new Scanner(optabFile)) {
                while (optabScanner.hasNextLine()) {
                    String line = optabScanner.nextLine();
                    String[] parts = line.split("-");
                    if (parts.length >= 2) {
                        code = parts[0];
                        mnemonic = parts[1];

                        if (opcode.equals(code)) {
                            locctr += 3;
                            found = true;
                            break;
                        }
                    }
                }
            }

            if (!found) {
                if (opcode.equals("WORD")) {
                    locctr += 3;
                } else if (opcode.equals("RESW")) {
                    locctr += 3 * Integer.parseInt(operand);
                } else if (opcode.equals("BYTE")) {
                    if (operand.charAt(0) == 'C') {
                        locctr += (operand.length() - 3); 
                    } else if (operand.charAt(0) == 'X') {
                        locctr += (operand.length() - 3) / 2;  
                    }
                } else if (opcode.equals("RESB")) {
                    locctr += Integer.parseInt(operand);
                }
            }

            if (inScanner.hasNext()) {
                label = inScanner.next();
                opcode = inScanner.next();
                operand = inScanner.next();
            }
        }

        inwriter.write(Integer.toHexString(locctr).toUpperCase() + "\t" + label + "\t" + opcode + "\t" + operand + "\n");
        inScanner.close();
        symtabWriter.close();
        inwriter.close();
    }
    catch (NoSuchElementException ex) {
            JOptionPane.showMessageDialog(null, "Error processing input: " + ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
    }
    catch (NullPointerException x) {
        x.printStackTrace();
    }

    length =  locctr - start;

    return length;
}



public static void passTwo(String label, String opcode, String operand, String code, String mnemonic, int programLength) throws IOException {
    String objcode = "", sym_addr;
    String start_addr = "", addr = " ";
    String program_name = "";
    String textr = "";
    int text_len = 0, instruction_count = 0, textrlength = 0;

    BufferedReader br1 = new BufferedReader(new FileReader(intermediateFile));
    BufferedReader br2 = new BufferedReader(new FileReader(optabFile));
    BufferedReader br3 = new BufferedReader(new FileReader(symtabFile));
    BufferedWriter objcodewriter = new BufferedWriter(new FileWriter("object_code.txt"));
    String[] parts;
    String line = br1.readLine();
    if (line != null && !line.trim().isEmpty()) {
        parts = line.split("\t");

        label = parts[0];
        opcode = parts[1];
        operand = parts[2];

        if (opcode.equals("START")) {
            program_name = label;
            start_addr = operand;
            String lengthex = String.format("%06X", programLength);
            objcodewriter.write("\n\nH^" + program_name + "^00" + start_addr + "^" + lengthex + "\n\n");
            line = br1.readLine();
            parts = line.split("\t");
            start_addr = parts[0];
            label = parts[1];
            opcode = parts[2];
            operand = parts[3];
        }

        textr = "T^00" + start_addr;
        int len = 0;

        while (!opcode.equals("END")) {
            if (opcode.charAt(0) != '.') {
                br2 = new BufferedReader(new FileReader("optab.txt"));
                boolean found = false;

                while ((line = br2.readLine()) != null) {
                    parts = line.split("-");
                    if (parts.length >= 2) {
                        code = parts[0];
                        mnemonic = parts[1];
                        if (opcode.equals(code)) {
                            found = true;
                            break;
                        }
                    }
                }

                if (found) {
                    if (!operand.equals("-")) {
                        br3 = new BufferedReader(new FileReader("symtab.txt"));
                        while ((line = br3.readLine()) != null) {
                            parts = line.split("\t");
                            if (operand.equals(parts[0])) {
                                operand = parts[1];
                                break;
                            }
                        }
                    } else {
                        operand = "00";
                    }
                    objcode = mnemonic + operand;
                } else if (opcode.equals("BYTE")) {
                    if (operand.startsWith("C")) {
                        StringBuilder str = new StringBuilder();
                        for (int i = 2; i < operand.length() - 1; i++) {
                            str.append(String.format("%02X", (int) operand.charAt(i)));
                        }
                        objcode = str.toString(); // No extra "^"
                        len += operand.length() - 3;
                    } else if (operand.startsWith("X")) {
                        objcode = operand.substring(2, operand.length() - 1); // No extra "^"
                        len += objcode.length() / 2;
                    }
                } else if (opcode.equals("WORD")) {
                    objcode = String.format("%06X", Integer.parseInt(operand)); // Ensure it's 6 digits
                    len += 3;
                } else {
                    objcode = "";
                }

                if (len + objcode.length() / 2 > 30 || instruction_count == 5) {
                    textr += "^" + String.format("%02X", textrlength / 2);
                    objcodewriter.write(gcode(textr));
                    textr = "\nT^00" + addr;
                    len = 0;
                    instruction_count = 0;
                    textrlength = 0;
                }

                textr += "^" + objcode;
                textrlength += objcode.length();
                instruction_count++;
            }
            line = br1.readLine();
            if (line != null) {
                parts = line.split("\t");
                addr = parts[0];
                label = parts[1];
                opcode = parts[2];
                operand = parts[3];
            }
        }

        if (instruction_count > 0) {
            textr += "^" + String.format("%02X", textrlength / 2);
        }
        objcodewriter.write(gcode(textr));
        objcodewriter.write("\n\n\nE^00" + start_addr + "\n");

        br1.close();
        br2.close();
        br3.close();
        objcodewriter.flush();
        objcodewriter.close();
    }
}

public static String gcode(String input) {
    if (input.length() > 0) {
        String lastchars = input.substring(input.length() - 2);
        String frontPart = input.substring(0, 9);
        String restPart = input.substring(9);
        String tRestPart = restPart.substring(0, restPart.lastIndexOf('^'));
        return frontPart + " ^ " + lastchars + " ^ " + tRestPart;
    }
    return "";
}

}



/*
COPY START 1000
** LDA ALPHA
** ADD ONE
** SUB TWO
** STA BETA
ALPHA BYTE C'CZXE'
ONE RESW 2
TWO WORD 5
BETA RESW 1
** END -


prog1 START 2000
** LDA FIVE
** STA ALPHA
** LDCH CHARZ
** STCH C1
ALPHA RESW 2
FIVE WORD 5
CHARZ BYTE C'Z'
C1 RESB 1
** END **


prog START 1000
FIRST LDA     NUM1        
** ADD     NUM2      
** STA     RESULT      
**       HLT    -            
NUM1    WORD    0005        
NUM2    WORD    0007        
RESULT  RESW    1          
**      END     FIRST
*/

