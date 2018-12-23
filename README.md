# bookshop
package com.nsu.jxy.service;

import java.io.IOException;
import java.io.PrintWriter;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.nsu.jxy.beans.User;
import com.nsu.jxy.dao.DBConn;
import com.nsu.jxy.dao.JDBCUtils;
import com.nsu.jxy.dao.UserDao;


/**
 * Servlet implementation class LoginServlet
 */
@WebServlet("/LoginServlet")
public class LoginServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    public LoginServlet() {
        super();
    }

	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		request.setCharacterEncoding("utf-8");
		String uname = request.getParameter("J_account");
		String upass = request.getParameter("J_code");
		String verifyCode = request.getParameter("verifyCode");
		PrintWriter pw = response.getWriter();
		if("cbHE".equals(verifyCode)) {
			String sql1 = "select * from user where username=? and PASSWORD=?";
			Connection connection;
			ResultSet rs = null;			
				try {
					connection = JDBCUtils.getConnection();
					PreparedStatement pst = connection.prepareStatement(sql1);
					pst.setString(1, uname);
					pst.setString(2, upass);
					rs = pst.executeQuery();
					request.getSession().setMaxInactiveInterval(3*60);
					response.setContentType("text/html;charset=utf-8");
					if(rs != null && rs.next()) {
						User user = UserDao.findUserByUsernameAndPassword(uname, upass);
						request.getSession().setAttribute("user", user);
						response.sendRedirect(request.getContextPath() + "/jsp/t2.jsp");					
					}else {
						response.sendRedirect(request.getContextPath() + "/jsp/href_login.jsp");
						pw.println("用户名或密码错误!");
					}
						
				} catch (SQLException e) {
					e.printStackTrace();
				}			
		}else {
			response.sendRedirect(request.getContextPath() + "/jsp/href_login.jsp");
			pw.println("验证码错误！");
		}
			
	}

	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		doGet(request, response);
	}

}

