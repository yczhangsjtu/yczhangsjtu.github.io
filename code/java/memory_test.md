---
title: Memory Test Game
---

```java
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.awt.geom.*;
import java.awt.image.*;
import java.util.Random;

public class MemoryTest extends JFrame implements ActionListener
{
	int list[];
	int level = 12;
	int t = 0;
	JButton buttons[] = new JButton[8];
	JButton start = new JButton("START");
	JButton reduce = new JButton("<");
	JButton increase = new JButton(">");
	JLabel lv = new JLabel(Integer.toString(level));
	Timer timer = new Timer(1000,this);
	Timer erase = new Timer(100,this);
	Random gen = new Random(System.currentTimeMillis());
	Color bcolor = new JButton("").getBackground();
	Color ncolor = Color.red;
	boolean player = false;

	MemoryTest()
	{
		super("Memory Test");
		setResizable(false);

		setLayout(new GridLayout(5,5));
		int bi = 0;

		lv.setFont(new Font("SansSerif", Font.BOLD, 20));
		lv.setHorizontalAlignment(JTextField.CENTER);

		start.addActionListener(this);
		reduce.addActionListener(this);
		increase.addActionListener(this);
		timer.setInitialDelay(1000);
		timer.setDelay(1000);
		timer.setRepeats(true);
		erase.setInitialDelay(200);
		erase.setRepeats(false);

		for (int i = 0; i < 5; i++)
		{
			for (int j = 0; j < 5; j++)
			{
				if (Math.abs(i-2)+Math.abs(j-2)==2)
				{
					buttons[bi++] = new JButton("");
					add(buttons[bi-1]);
					buttons[bi-1].addActionListener(this);
				}
				else if (i==2 && j==1)
					add(reduce);
				else if (i==2 && j==3)
					add(increase);
				else if (i==2 && j==2)
					add(start);
				else if (i==1 && j==2)
					add(lv);
				else
					add(new Label(""));
			}
		}
		setSize(500,500);
		setVisible(true);
	}

	public void paint(Graphics g)
	{
		super.paint(g);
	}

	public void actionPerformed(ActionEvent ae)
	{
		if (ae.getSource() == start)
		{
			if (level <= 0) level = 1;
			list = new int[level];
			t = 0;
			timer.start();
		}
		else if (ae.getSource() == reduce)
		{
			if (level > 1) level --;
			else level = 1;
			lv.setText(Integer.toString(level));
		}
		else if (ae.getSource() == increase)
		{
			if (level > 0) level ++;
			else level = 1;
			lv.setText(Integer.toString(level));
		}
		else if (ae.getSource() == timer)
		{
			if (t < 0)
			{
				t = 0;
				timer.stop();
			}
			else if (t < level)
			{
				list[t] = gen.nextInt(8);
				buttons[list[t]].setBackground(ncolor);
				erase.start();
				t++;
			}
			else
			{
				t = 0;
				timer.stop();
				JOptionPane.showMessageDialog(null,"Player's turn.");
				player = true;
				for (int i = 0; i < 8; i++)
				{
					buttons[i].setBackground(bcolor);
				}
			}
		}
		else if(ae.getSource()==erase)
		{
			for (int i = 0; i < 8; i++)
			{
				buttons[i].setBackground(bcolor);
			}
		}
		else
		{
			if (player)
			{
				if(t >= 0 && t < level)
				{
					for (int i = 0; i < 8; i++)
					{
						if (ae.getSource() == buttons[i])
						{
							if(list[t++] != i)
							{
								JOptionPane.showMessageDialog(null,"Wrong!");
								player = false;
								t = 0;
							}
						}
					}
					if(t >= level)
					{
						JOptionPane.showMessageDialog(null,"Correct!");
						player = false;
						t = 0;
						level ++;
						lv.setText(Integer.toString(level));
					}
				}
			}
		}
	}

	public static void main(String arg[])
	{
		MemoryTest win = new MemoryTest();
		win.addWindowListener( new WindowAdapter()
				{
				public void windowClosing( WindowEvent e )
				{
				System.exit(0);
				}
				});
	}

}
```
