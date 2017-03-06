---
title: Reflex Game
---

```java
import javax.swing.*;
import java.awt.event.*;
import java.awt.*;
import java.awt.geom.*;
import java.awt.image.*;
import java.util.Random;

public class Reflex extends Frame
    implements ActionListener, MouseListener,
               AdjustmentListener, ReportListener
{
    JButton start = new JButton("START");
    JButton stop = new JButton("STOP");
    JLabel score = new JLabel("Score: ");
    JLabel mistk = new JLabel("Mistake: ");
    JLabel intvl = new JLabel("Interval: ");
    JLabel rtime = new JLabel("Remain Time: ");
    Scrollbar ibar = new Scrollbar(Scrollbar.HORIZONTAL, 1000, 1, 100, 1001);
    Scrollbar tbar = new Scrollbar(Scrollbar.HORIZONTAL, 60, 1, 0, 301);
    Timer timer = new Timer(1000,this);
    GridPanel grid = new GridPanel();
    boolean clicked = false;
    int scr = 0;
    int mtk = 0;
    
    Reflex()
    {
        super("Reflex");
        setSize(550,430);
        setResizable(false);
        
        setLayout(new BorderLayout());
        addMouseListener(this);
        
        Panel panel = new Panel();
        grid.addReportListener(this);
        grid.setLayout(new GridLayout(1,1));
        panel.setLayout(new GridLayout(16,1));
        panel.setPreferredSize(new Dimension(130,400));
        panel.add(start);
        panel.add(stop);
        panel.add(score);
        panel.add(mistk);
        panel.add(ibar);
        panel.add(intvl);
        panel.add(tbar);
        panel.add(rtime);
        
        timer.setRepeats(true);
        timer.setActionCommand("Change");
        
        start.addActionListener(this);
        stop.addActionListener(this);
        ibar.addAdjustmentListener(this);
        tbar.addAdjustmentListener(this);
        
        add(grid, BorderLayout.WEST);
        add(panel, BorderLayout.EAST);
        
        score.setText("Score: " + Integer.toString(scr));
        mistk.setText("Mistake: " + Integer.toString(mtk));
        intvl.setText("Interval: " +
            Double.toString(ibar.getValue()/1000.0) + " s");
        rtime.setText("Remain Time: " + Integer.toString(tbar.getValue()));
            
        setVisible(true);
    }
    
    public void paint(Graphics g)
    {
        super.paint(g);
    }
    
    public void actionPerformed(ActionEvent ae)
    {
        String cmd = ae.getActionCommand();
        if(cmd != null)
        {
            if(cmd == "START")
            {
                timer.setDelay(ibar.getValue());
                timer.setInitialDelay(ibar.getValue());
                timer.start();
                grid.start();
            }
            else if(cmd == "STOP")
            {
                scr = 0;
                mtk = 0;
                score.setText("Score: " + Integer.toString(scr));
                mistk.setText("Mistake: " + Integer.toString(mtk));
                timer.stop();
                grid.stop();
            }
            else if(cmd == "Change")
            {
                if(tbar.getValue() > 0)
                {
                    grid.reset();
                    tbar.setValue(tbar.getValue()-1);
                    rtime.setText("Remain Time: " +
                        Integer.toString(tbar.getValue()));
                    clicked = false;
                }
                else
                {
                    JOptionPane.showMessageDialog
                        (null, "You clicked " + scr + " times, and made " +
                        mtk + " mistakes.");
                    scr = 0;
                    mtk = 0;
                    score.setText("Score: " + Integer.toString(scr));
                    mistk.setText("Mistake: " + Integer.toString(mtk));
                    timer.stop();
                    grid.stop();
                }
            }
        }
    }
    
    public void mouseClicked(MouseEvent e)
    {
    }
    
    public void mousePressed(MouseEvent e)
    {
    }
    
    public void mouseReleased(MouseEvent e)
    {
    }
    
    public void mouseEntered(MouseEvent e)
    {
    }
    
    public void mouseExited(MouseEvent e)
    {
    }
    
    public void panelClicked(ReportEvent e)
    {
        if(e.getHit())
        {
            if(!clicked)
            {
                clicked = true;
                scr ++;
            }
        }
        else
            mtk ++;
        score.setText("Score: " + Integer.toString(scr));
        mistk.setText("Mistake: " + Integer.toString(mtk));
    }
    
    public void adjustmentValueChanged(AdjustmentEvent e)
    {
        if(e.getAdjustable()==ibar)
        {
            intvl.setText("Interval: " +
                Double.toString(ibar.getValue()/1000.0) + " s");
        }
        else if(e.getAdjustable()==tbar)
        {
            rtime.setText("Remain Time: " + Integer.toString(tbar.getValue()));
        }
    }
    
    public static void main(String args[])
    {
        Reflex win = new Reflex();
        win.addWindowListener( new WindowAdapter()
        {
            public void windowClosing( WindowEvent e )
            {
                System.exit(0);
            }
        }
        );
    }
}

class GridPanel extends JPanel implements MouseListener, ActionListener
{
    int x0 = 0, y0 = 0;
    int nw = 8, nh = 8;
    int size = 50;
    int W = size * nw, H = size * nh;
    int cI = 0, cJ = 0;
    int mI = 0, mJ = 0;
    boolean showIt = false;
    boolean mistake = false;
    boolean correct = false;
    Timer timer = new Timer(100,this);
    Random gen = new Random(System.currentTimeMillis());
    
    ReportListener listener;
    
    public GridPanel()
    {
        setPreferredSize(new Dimension(W,H));
        addMouseListener(this);
        timer.setActionCommand("Erase");
        timer.setRepeats(false);
        timer.setInitialDelay(100);
        repaint();
    }
    
    public void paintComponent(Graphics g)
    {
        super.paintComponent(g);
        
        Graphics2D g2d = (Graphics2D) g;
        
        g2d.setPaint(Color.yellow);
        g2d.setStroke( new BasicStroke( 3.0f ) );
        for(int i = 0; i <= nw; i++)
            g2d.draw( new Line2D.Double(x0+i*size,y0,x0+i*size,y0+H) );
        for(int i = 0; i <= nh; i++)
            g2d.draw( new Line2D.Double(x0,y0+i*size,x0+W,y0+i*size) );
        
        if(showIt)
        {
            if(mistake)
            {
                g2d.setPaint(Color.red);
                g2d.fillRect(mI*size, mJ*size, size, size);
            }
            else
            {
                if(correct)
                    g2d.setPaint(Color.yellow);
                else
                    g2d.setPaint(Color.green);
                g2d.fillRect(cI*size, cJ*size, size, size);
            }
        }
    }
    
    public void addReportListener(ReportListener l)
    {
        listener = l;
    }
    
    public void mouseClicked(MouseEvent e)
    {
        int x = e.getX();
        int y = e.getY();
        int i = (x-x0)/size;
        int j = (y-y0)/size;
        
        if(showIt)
        {
            correct = i==cI && j==cJ;
            mistake = !correct;
            ReportEvent re = new ReportEvent(this, correct);
            listener.panelClicked(re);
            if(mistake)
            {
                mI = i;
                mJ = j;
            }
            timer.start();
            repaint();
        }
    }
    
    public void start()
    {
        showIt = true;
        reset();
        repaint();
    }
    
    public void stop()
    {
        showIt = false;
        repaint();
    }
    
    public void reset()
    {
        cI = gen.nextInt(nw);
        cJ = gen.nextInt(nh);
        repaint();
    }
    
    public void actionPerformed(ActionEvent e)
    {
        String cmd = e.getActionCommand();
        if(cmd != null)
        {
            if(cmd == "Erase")
            {
                mistake = false;
                correct = false;
                repaint();
            }
        }
    }
    
    public void mousePressed(MouseEvent e)
    {
    }
    
    public void mouseReleased(MouseEvent e)
    {
    }
    
    public void mouseEntered(MouseEvent e)
    {
    }
    
    public void mouseExited(MouseEvent e)
    {
    }
    
    
}

class ReportEvent extends java.util.EventObject
{
    boolean hit = true;
    public ReportEvent(Object source, boolean h)
    {
        super(source);
        hit = h;
    }
    boolean getHit(){return hit;}
}

interface ReportListener extends java.util.EventListener
{
    public void panelClicked(ReportEvent e);
}
```
