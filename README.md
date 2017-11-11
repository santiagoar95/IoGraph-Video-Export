# IoGraph-Video-Export

package core;
import java.awt.BorderLayout;
import java.awt.Frame;
import java.awt.GraphicsDevice;
import java.awt.GraphicsEnvironment;
import java.awt.MouseInfo;
import java.awt.Point;
import java.awt.event.WindowAdapter;
import java.awt.event.WindowEvent;

import processing.core.PApplet;

public class MousePath extends PApplet {

	public static void main(String args[]) {
		Frame f = new Frame("Mouse Path. [S] Save. [R] Restart. [D] Dots recording toggle. © Anatoly Zenkov (anatolyzenkov.com)");
		GraphicsEnvironment e = GraphicsEnvironment.getLocalGraphicsEnvironment();
		GraphicsDevice s = e.getDefaultScreenDevice();
		float scale = Math.min((float) s.getDisplayMode().getWidth(), 1280) / (float) s.getDisplayMode().getWidth();
		//scale = 1;
		f.setSize((int) (s.getDisplayMode().getWidth() * scale), (int) (s.getDisplayMode().getHeight() * scale));
		f.addWindowListener(new WindowAdapter() {
			public void windowClosing(WindowEvent we) {
				System.exit(0);
			}
		});
		f.setResizable(false);
		f.setLayout(new BorderLayout());
		MousePath p = new MousePath();
		f.add(p, BorderLayout.CENTER);
		p.init();
		f.setVisible(true);
	}

	private float _scale = -1f;
	private float _radius;
	private boolean _stops;
	private Point _prevP;
	private Point _newP;
	private Point _stopP;

	public MousePath() {
		this.start();
	}
	
	public MousePath(float scale) {
		_scale = scale;
		this.start();
	}

	public void setup() {
		GraphicsEnvironment e = GraphicsEnvironment.getLocalGraphicsEnvironment();
		GraphicsDevice s = e.getDefaultScreenDevice();
		if (_scale == -1f) _scale = Math.min((float) s.getDisplayMode().getWidth(), 1280f) / (float) s.getDisplayMode().getWidth();
		_stops = true;
		size((int) (s.getDisplayMode().getWidth() * _scale), (int) (s.getDisplayMode().getHeight() * _scale));
		_newP = MouseInfo.getPointerInfo().getLocation();
		_prevP = _newP;
		_stopP = _newP;
		strokeWeight(.2f*_scale);
		frameRate(60);
		smooth();
		background(255);
	}

	public void draw() {
		_newP = MouseInfo.getPointerInfo().getLocation();
		line(_newP.x * _scale, _newP.y * _scale, _prevP.x * _scale, _prevP.y * _scale);
		if (!_stops) {
			_prevP = _newP;
			return;
		}
		int dx = _newP.x - _stopP.x;
		int dy = _newP.y - _stopP.y;
		int d = dx * dx + dy * dy;
		if (d < 400) {
			_radius += .3;
			_prevP = _newP;
			return;
		}
		if (_radius > 20) {
			fill(255, 200f * max(0f, 1f - sqrt(_radius) / 20));
			ellipse(_stopP.x * _scale, _stopP.y * _scale, _radius * .5f* _scale, _radius * .5f* _scale);
			fill(0);
			ellipse(_stopP.x * _scale, _stopP.y * _scale, sqrt(_radius)* _scale, sqrt(_radius)* _scale);
			noFill();
		}
		_prevP = _newP;
		_stopP = _newP;
		_radius = 0;
	}

	public void keyReleased() {
		if (key == 's' || key == 'S') {
			String n = nf(hour(), 2) + "_" + nf(minute(), 2) + "_" + nf(second(), 2);
			save("mousePath-" + n + ".tif");
			status("Saved to 'mousePath_" + n + ".tif'");
			return;
		}
		if (key == 'r' || key == 'R') {
			background(255);
		}
		if (key == 'd' || key == 'D') {
			_stops = !_stops;
		}
	}

	private static final long serialVersionUID = 1L;

}

