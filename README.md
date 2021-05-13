- 👋 Hi, I’m @hun21-0512
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...

<!---
hun21-0512/hun21-0512 is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
package miniCompiler;

import java.awt.BorderLayout;
import java.awt.EventQueue;
import java.awt.Font;
import javax.swing.JFileChooser;
import javax.swing.JFrame;
import javax.swing.JPanel;
import javax.swing.border.EmptyBorder;
import javax.swing.filechooser.FileNameExtensionFilter;
import javax.swing.JOptionPane;
import javax.swing.JScrollPane;
import javax.swing.JMenuBar;
import javax.swing.JMenuItem;
import javax.swing.JMenu;
import javax.swing.JTextArea;
import java.awt.event.ActionListener;
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.File;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStream;
import java.util.regex.Matcher;
import java.util.regex.Pattern;
import java.awt.event.ActionEvent;

public class Mymemo extends JFrame implements ActionListener {
	public String class_name;
   private JMenuItem menuNew;
   private JMenuItem menuOpen;
   private JMenuItem menuSave;
   private JMenuItem menuExit;

   private JMenuItem menuCopy;
   private JMenuItem menuPaste;
   private JMenuItem menuCut;
   private JMenuItem menuCut_1;
   private JMenuItem menu런;
   private JMenuItem who;
   
   private JMenuBar menuBar;
   private JPanel contentPane;
   private JMenu fileM;
   private JMenu comM;
   private JMenu menuRun;
   
   private JTextArea textArea;
   private JTextArea textAreaConsole;
   String copyText;

   String docuPath;

   class ThreadCompiler extends Thread {
      public void run() {
         try {
            comPile();
         } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
         } catch (InterruptedException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
         }
      }

   }

   /**
    * Launch the application.
    */

   public void comPile() throws IOException, InterruptedException {
      Process pc = null;

      try {
         this.textAreaConsole.setText("");
         
         // pc =
         // rt.exec("C:\\Program Files\\Java\\jdk-15.0.2\\bin\\javac.exe");
         //C:\Program Files\Java\jdk-15.0.2\bin\javac.exe
         ProcessBuilder pb = new ProcessBuilder(
               "C:\\Program Files\\Java\\jdk-15.0.2\\bin\\javac.exe",
               docuPath);
         pc = pb.start();
         pc.waitFor();
         InputStream is = pc.getErrorStream();
         InputStreamReader isr = new InputStreamReader(is);
         BufferedReader br = new BufferedReader(isr);
         String errorStr = "";
         while (true) {
            String line = br.readLine();
            if (line == null)
               break;
            errorStr += line+"\n";
         }
         this.textAreaConsole.setText(errorStr);
         JOptionPane.showMessageDialog(this, "Compile success");

      } catch (IOException e) {
         e.printStackTrace();
      } finally {
         pc.destroy();
      }
   }
   
   class ThreadRun extends Thread {
      public void run() {
         try {
            runJava();
         } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
         } catch (InterruptedException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
         }
      }

   }
   
   public void runJava() throws IOException, InterruptedException {
      Process pc = null;

      try {
         this.textAreaConsole.setText("");
         
         File file = new File(docuPath);
         int indexDot = file.getName().indexOf(".");
         String name = file.getName().substring(0, indexDot);
         String path = file.getParent()+File.separator+name;
         ProcessBuilder pb = new ProcessBuilder(
               "C:\\Program Files\\Java\\jdk-15.0.2\\bin\\javaw.exe",
               name);
         pb.directory(new File(file.getParent()));
         File console = new File(file.getParent()+File.separator+"Console.txt");
         pb.redirectOutput(console);
         pc = pb.start();
         pc.waitFor();
         
         String str = open(console);
         this.textAreaConsole.setText(str);
         JOptionPane.showMessageDialog(this, "Run success");

      } catch (IOException e) {
         e.printStackTrace();
      } finally {
         pc.destroy();
      }
   }

   public static void main(String[] args) {
      EventQueue.invokeLater(new Runnable() {
         public void run() {
            try {
               Mymemo frame = new Mymemo();
               frame.setVisible(true);
               // comPile();
            } catch (Exception e) {
               e.printStackTrace();
            }
         }
      });

      // comPile();
   }
   
   public void menusv(){
	   JFileChooser chooser = new JFileChooser();
       FileNameExtensionFilter filter = new FileNameExtensionFilter(
             "Java Files", "java");
       chooser.setFileFilter(filter);
       int returnVal = chooser.showSaveDialog(this);
       if (returnVal == JFileChooser.APPROVE_OPTION) {
          docuPath = chooser.getSelectedFile().getAbsolutePath();
          save(new File(docuPath));
       }
   }
   /**
    * Create the frame.
    */
   public Mymemo() {
      setTitle("Mini compiler");
      setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
      setBounds(100, 100, 700, 600);

      contentPane = new JPanel();
      contentPane.setBorder(new EmptyBorder(5, 5, 5, 5));
      this.setContentPane(contentPane);
      
      contentPane.setLayout(new BorderLayout());

      textArea = new JTextArea();
      textArea.setFont(new Font(Font.SERIF, Font.PLAIN, 18));
      JScrollPane sp1 = new JScrollPane(textArea); 
      contentPane.add(sp1, BorderLayout.CENTER);
      
      textAreaConsole = new JTextArea(10, 30);
      textArea.setFont(new Font(Font.SERIF, Font.PLAIN, 18));
      JScrollPane sp2 = new JScrollPane(textAreaConsole); 
      contentPane.add(sp2, BorderLayout.SOUTH);
      
      menuBar = new JMenuBar();
      menuBar.setBounds(0, 0, 434, 23);
      this.setJMenuBar(menuBar);

      fileM = new JMenu("File");
      menuBar.add(fileM);

      // 새문서
      menuNew = new JMenuItem("새문서");
      fileM.add(menuNew);

      // 열기
      menuOpen = new JMenuItem("열기");
      fileM.add(menuOpen);

      menuSave = new JMenuItem("저장");
      fileM.add(menuSave);

      menuExit = new JMenuItem("종료");
      fileM.add(menuExit);

      JMenu editM = new JMenu("Edit");
      menuBar.add(editM);

      menuCopy = new JMenuItem("복사");
      editM.add(menuCopy);

      menuPaste = new JMenuItem("붙여넣기");
      editM.add(menuPaste);

      menuCut = new JMenuItem("잘라내기");
      editM.add(menuCut);

      comM = new JMenu("Compile");
      menuBar.add(comM);
      
      menuRun = new JMenu("Run");
      menuBar.add(menuRun);


      menuCut_1 = new JMenuItem("컴파일");
      comM.add(menuCut_1);
      
      menu런 = new JMenuItem("런");
      menuRun.add(menu런);
      
      who = new JMenuItem("제작자");
      menuRun.add(who);

      // 메뉴 활성화를 위해 리스너 장착
      menuExit.addActionListener(this);
      editM.addActionListener(this);
      menuNew.addActionListener(this);
      menuCopy.addActionListener(this);
      menuPaste.addActionListener(this);
      menuCut.addActionListener(this);
      menuCut_1.addActionListener(this);
      menuOpen.addActionListener(this);
      menuSave.addActionListener(this);
      who.addActionListener(this);
      menu런.addActionListener(this);
   }
   
   
   void save(File file) {
      try {
         FileWriter fw = new FileWriter(file);
         BufferedWriter bw = new BufferedWriter(fw);
         String text = textArea.getText();
         bw.write(text);
         bw.close();
         JOptionPane.showMessageDialog(this, "Save success");
      }
      catch(Exception e) {
         
      }      
   }
   
   String open(File file) {
      try {
         String r = "";
         FileReader fw = new FileReader(file);
         BufferedReader bw = new BufferedReader(fw);
         while (true) {
            String line = bw.readLine();
            if (line==null) break;
            r += line+"\n";            
         }
         bw.close();
         JOptionPane.showMessageDialog(this, "Open success");
         return r;
      }
      catch(Exception e) {
         
      }
      return null;
   }

   @Override
   public void actionPerformed(ActionEvent e) {

      if (e.getSource() == menuExit) { // 종료
         System.exit(0);
         
      } 
      else if (e.getSource() == menuNew) {
    	  textArea.setText("");
          setTitle("제목없음 - 메모장");
      }
     // 아래는 복사.
      else if (e.getSource() == menuCopy) {
         copyText = textArea.getSelectedText(); // 선택된 부분만 복사
      }
      // 붙여넣기
      else if (e.getSource() == menuPaste) {
         int position = textArea.getCaretPosition(); // 위치 잡기
         textArea.insert(copyText, position);
      }
      // 잘라내기
      else if (e.getSource() == menuCut) {
         int start = textArea.getSelectionStart();
         int end = textArea.getSelectionEnd();
         textArea.replaceRange("", start, end);
      }

      else if (e.getSource() == menuOpen) {
         JFileChooser chooser = new JFileChooser();
         FileNameExtensionFilter filter = new FileNameExtensionFilter(
               "Java Files", "java");
         chooser.setFileFilter(filter);
         int returnVal = chooser.showOpenDialog(this);
         if (returnVal == JFileChooser.APPROVE_OPTION) {
            docuPath = chooser.getSelectedFile().getAbsolutePath();
            String str = open(new File(docuPath));
            this.textArea.setText(str);
         }
      } else if (e.getSource() == menuSave) {
    	  menusv();
      }

      else if (e.getSource() == menuCut_1) {
    	  String class_name = textArea.getText();
    	  
    	  if (class_name.contains("203620") || class_name.contains("신동훈") || class_name.contains("김혜선")||class_name.contains("189138")) {
    		  textAreaConsole.setText("콘솔창에 제작자를 입력하셨네요");
    		  return;
    	  }
    	  
    	  Pattern pattern = Pattern.compile("(\\bpublic class \\b)(.*?)[ {]");
    	  Matcher matcher = pattern.matcher(class_name);
			if (matcher.find()) {
				class_name = matcher.group(2);
			} else {
				textAreaConsole.setText("Class 이름 추출 실패");
				return;
			}
			
			JFileChooser chooser = new JFileChooser();
			chooser.setSelectedFile(new File(class_name+".java"));
			
            ThreadCompiler t = new ThreadCompiler();
            t.start();

      }
      else if (e.getSource() == menu런) {
         // 기능구현
         // System.out.println("-");
         ThreadRun t = new ThreadRun();
         t.start();

      }
      else if (e.getSource() == who) {
    	  JOptionPane.showMessageDialog(this,  "189138 김혜선 \n203620 신동훈");
      }

   }
}
