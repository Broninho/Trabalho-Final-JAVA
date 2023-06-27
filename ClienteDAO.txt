import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;

public class ClienteDAO {

	public Cliente consultar() {
		//SELECT
		return null;
	}
	
	public void setDep(String cpf) {
	    ConectaBD con = new ConectaBD();
	    String sql = "UPDATE pessoa SET dep = 1 where CPF = "+cpf;
	    try {
	        PreparedStatement pst = con.getConexao().prepareStatement(sql);
	        pst.executeQuery();
	    } catch (SQLException e) {
	        System.out.println(e.getMessage());
	    } 
	    
	}
	
	public Cliente consultarPCPF(String id) {
	    ConectaBD con = new ConectaBD();
	    String sql = "SELECT * FROM pessoa WHERE cpf = ?";
	    Cliente p = null;
	    try {
	        PreparedStatement pst = con.getConexao().prepareStatement(sql);
	        pst.setString(1, id);
	        ResultSet rs = pst.executeQuery();
	        if (rs.next()) {
	        	String nome = rs.getString("nome");
	        	String cpf = rs.getString("cpf");
	            p = new Cliente();
	            p.setNome(nome);
	            p.setCpf(cpf);
	            //System.out.println(p.getCpf());
	        }
	    } catch (SQLException e) {
	        System.out.println(e.getMessage());
	    } 
	    
	    return p;
	}
	
	public ArrayList<Cliente> consultarTodos(){
		ConectaBD con = new ConectaBD();
		String sql = "SELECT * FROM pessoa";
		ArrayList<Cliente> lista = new ArrayList<Cliente>();
		try {
			PreparedStatement pst = con.getConexao().prepareStatement(sql);
			ResultSet rs = pst.executeQuery();
			while(rs.next()) {
				Cliente pessoa = new Cliente();
				String nome = rs.getString("nome");
				String cpf = rs.getString("cpf");
				pessoa.setNome(nome);
				pessoa.setCpf(cpf);
				lista.add(pessoa);
			}
			
		}catch(SQLException e) {
			System.out.println(e.getMessage());
		}
		
		return lista;
	}
	
	public void inserir(Cliente p) {
		ConectaBD  conn = new ConectaBD();
		String sql = "INSERT INTO pessoa (nome,cpf,dep) VALUES(?,?,?)";
		try {
			PreparedStatement prep = conn.getConexao().prepareStatement(sql);
			prep.setString(1, p.getNome());
			prep.setString(2, p.getCpf());
			prep.setBoolean(3, p.isDep());
			prep.execute();
			System.out.println("Inserido.");
		} catch (SQLException e) {	
			System.out.println(e.getMessage());
		}	
	}
	
	public void setDepCliente(Cliente d) {
		ConectaBD  conn = new ConectaBD();
		Cliente t = consultarPCPF(d.getCpf());
		String sql = "UPDATE pessoa SET dep = 1 WHERE cpf = "+d.getCpf();
		try {
			PreparedStatement prep = conn.getConexao().prepareStatement(sql);
			prep.execute();
			System.out.println("ATUALIZADO ==================================.");
		} catch (SQLException e) {	
			System.out.println(e.getMessage());
		}	
	}
	
	public void setDepCartao(Cartao c,Cliente d) {
		ConectaBD  conn = new ConectaBD();
		Cliente t = consultarPCPF(d.getCpf());
		String sql = "UPDATE cartao SET dep = 1 WHERE cpf = "+d.getCpf();
		String sql1 = "UPDATE cartao SET cpfPessoa = ? WHERE id = "+c.getId();
		try {
			PreparedStatement prep = conn.getConexao().prepareStatement(sql);
			prep.execute();
			prep = conn.getConexao().prepareStatement(sql1);
			prep.setString(1, d.getCpf());
			prep.execute();
			System.out.println("ATUALIZADO ==================================.");
		} catch (SQLException e) {	
			System.out.println(e.getMessage());
		}	
	}
	
}

