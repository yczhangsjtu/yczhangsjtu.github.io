---
title: Reaction Test Game
---

```java
import javax.swing.*;
import java.awt.event.*;
import java.awt.*;
import java.awt.geom.*;
import java.awt.image.*;
import java.util.Random;

public class Reaction extends JFrame implements ActionListener, MouseListener
{
	JButton start = new JButton("Start");
	boolean filled = false;
	Timer timer = new Timer(1000,this);
	long time;
	Random gen = new Random(System.currentTimeMillis());
	Reaction()
	{
		super("Reaction");
		setSize(200,200);
		setResizable(false);
		start.addActionListener(this);
		addMouseListener(this);
		add(start);
		timer.setRepeats(false);
		timer.setActionCommand("Fill");
		setVisible(true);
	}
	
	public void paint( Graphics g )
	{
		Graphics2D g2d = (Graphics2D) g;
		
		if(filled)
		{
			g2d.setPaint(Color.red);
		}
		else
		{
			g2d.setPaint(Color.white);
		}
		g2d.fillRect( 0,0,200,200 );
	}
	
    public void actionPerformed(ActionEvent ae)
    {
		String cmd = ae.getActionCommand();
		if(cmd!=null)
		{
			if(cmd.equals("Start"))
			{
				start.setVisible(false);
				timer.setInitialDelay((int)(gen.nextDouble()*5000));
				timer.start();
			}
			else if(cmd.equals("Fill"))
			{
				filled = true;
				repaint();
				time = System.currentTimeMillis();
			}
		}
	}
	
	public void mouseClicked(MouseEvent e)
	{
	}
	
	public void mouseEntered( MouseEvent e )
	{
	}
	
	public void mousePressed( MouseEvent e )
	{
		if(filled)
		{
			long t = System.currentTimeMillis() - time;
			JOptionPane.showMessageDialog(
				null, "Reflection time: " + Long.toString(t) + " ms");
		}
		else
		{
			JOptionPane.showMessageDialog(
				null, "The color has not changed yet!");
		}
		filled = false;
		start.setVisible(true);
		repaint();
	}
	
	public void mouseReleased( MouseEvent e )
	{
	}
	
	public void mouseExited( MouseEvent e )
	{
	}
	
	public static void main( String args[])
	{
		Reaction win = new Reaction();
		
		win.addWindowListener( new WindowAdapter()
		{
			public void windowClosing( WindowEvent e)
			{
				System.exit(0);
			}
		}
		);
	}
}
```
