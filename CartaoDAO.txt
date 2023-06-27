
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;

public class CartaoDAO {
	
	public ArrayList<Cartao> consultarCartao(String cpf) {
		  ConectaBD con = new ConectaBD();
		    String sql = "SELECT * FROM cartao WHERE cpfPessoa = ?";
		    ArrayList<Cartao> p = new ArrayList<>();
		    try {
		        PreparedStatement pst = con.getConexao().prepareStatement(sql);
		        pst.setString(1, cpf);
		        ResultSet rs = pst.executeQuery();
		        while (rs.next()) {
		        	Cartao c = new Cartao();
		        	c.setNumCartao(rs.getString("numero"));
		        	c.setDataVal(rs.getString("validade"));
		        	c.setCodSeg(rs.getString("cod"));
		        	c.setLimite(rs.getFloat("limite"));
		        	c.setSaldo(rs.getFloat("saldo"));
		        	c.setPagamento(rs.getFloat("pagamento"));
					c.setDep(rs.getBoolean("dep"));
					c.setId(rs.getInt("id"));
					p.add(c);
		            //System.out.println(p.getCpf());
		        }
		    } catch (SQLException e) {
		        System.out.println(e.getMessage());
		    } 
		    
		    return p;
	} 

	public Cartao consultarCartaoN(String num) {
		  ConectaBD con = new ConectaBD();
		    String sql = "SELECT * FROM cartao WHERE numero = ?";
			Cartao c = new Cartao();
		    try {
		        PreparedStatement pst = con.getConexao().prepareStatement(sql);
		        pst.setString(1, num);
		        ResultSet rs = pst.executeQuery();
		        while (rs.next()) {
		        	
		        	c.setNumCartao(rs.getString("numero"));
		        	c.setDataVal(rs.getString("validade"));
		        	c.setCodSeg(rs.getString("cod"));
		        	c.setLimite(rs.getFloat("limite"));
		        	c.setSaldo(rs.getFloat("saldo"));
		        	c.setPagamento(rs.getFloat("pagamento"));
					c.setDep(rs.getBoolean("dep"));
					c.setId(rs.getInt("id"));
		            //System.out.println(p.getCpf());
		        }
		    } catch (SQLException e) {
		        System.out.println(e.getMessage());
		    } 
		    
		    return c;
	} 

	public void transacao(float v,String cpf,Cartao cartao) {
	    ConectaBD con = new ConectaBD();
		int id = cartao.getId();
	    float pag = cartao.getPagamento()+v;
		float limite = cartao.getLimite();
		
	    String sql = "UPDATE cartao SET pagamento = ? where cpfPessoa = "+cpf+" and id = "+id;
		String sql1 = "UPDATE cartao SET limite = ? where cpfPessoa = "+cpf+" and id = "+id;
	    try {
	        PreparedStatement pst = con.getConexao().prepareStatement(sql);
	        pst.setFloat(1, pag);
	        pst.executeQuery();
			pst = con.getConexao().prepareStatement(sql1);
	        pst.setFloat(1, limite-pag);
	        pst.executeQuery();
	    } catch (SQLException e) {
	        System.out.println(e.getMessage());
	    } 
	}



	public void pagarFat(String cpf, Cartao cartao) {
	    ConectaBD con = new ConectaBD();
	    int id = cartao.getId();
	    float pag = cartao.getPagamento();
		float limite = cartao.getLimite();
		float saldo = cartao.getSaldo();
		saldo -= pag;
		limite += pag;
		pag = 0;
	    String sql = "UPDATE cartao SET pagamento = ? where cpfPessoa = "+cpf+" and id = "+id;
		String sql1 = "UPDATE cartao SET limite = ? where cpfPessoa = "+cpf+" and id = "+id;
		String sql2 = "UPDATE cartao SET saldo = ? where cpfPessoa = "+cpf+" and id = "+id;
	    try {
	        PreparedStatement pst = con.getConexao().prepareStatement(sql);
	        pst.setFloat(1, pag);
	        pst.executeQuery();

			pst = con.getConexao().prepareStatement(sql1);
	        pst.setFloat(1, limite);
	        pst.executeQuery();

			pst = con.getConexao().prepareStatement(sql2);
	        pst.setFloat(1, saldo);
	        pst.executeQuery();
	    } catch (SQLException e) {
	        System.out.println(e.getMessage());
	    } 
	}
	
	public void inserir(Cartao c,String cpf) {
		ConectaBD  conn = new ConectaBD();
		String sql = "INSERT INTO cartao "
				+ "(id,numero,validade,cod,limite,saldo,pagamento,dep,cpfPessoa) VALUES(?,?,?,?,?,?,?,?,?)";
		String sql1 = "SELECT MAX(cartao.id) from cartao";
		try {
			PreparedStatement prep = conn.getConexao().prepareStatement(sql1);
			ResultSet rs = prep.executeQuery();
			int i;
			if(rs.next()){
				i = rs.getInt("MAX(cartao.id)");
			}else{
				i = 1;
			}
			
			prep = conn.getConexao().prepareStatement(sql);
			prep.setInt(1, i+1);
			prep.setString(2, c.getNumCartao());
			prep.setString(3, c.getDataVal());
			prep.setString(4, c.getCodSeg());
			prep.setFloat(5, c.getLimite());
			prep.setFloat(6, c.getSaldo());
			prep.setFloat(7, c.getPagamento());
			prep.setBoolean(8, c.isDep());
			prep.setString(9, cpf);
			
			prep.execute();
			System.out.println("Inserido.");
		} catch (SQLException e) {	
			System.out.println(e.getMessage());
		}
		
	}
	
}