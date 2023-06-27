import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
                                      
public class ConectaBD {
	private Connection conexao;
	
	public ConectaBD() {
		String url = "jdbc:mariadb://localhost:3306/banco";
		String user = "root";
		String pass = "root";
		try {
			conexao = DriverManager.getConnection(url, user, pass);
			//System.out.println("Conectado.");
		} catch (SQLException e) {
			e.printStackTrace();
			System.out.println(e.getMessage());
		}
	}
	
	public Connection getConexao() {
		return conexao;
	}
	
}
