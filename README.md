# Conexao-com-Banco-de-Dados
Connection String

Criei esse repository com o objetivo de apresentar como fazer uma Connection String, mas antes vamos conceituar o que é Connection String, ou seja, a Connection String define como vamos nos conectar ao banco de dados, qual usuário e banco vamos utilizar e outros recursos de segurança.

Em meus estudos encontrei vários tipos de conexão, seja com a criação de uma classe ou utilizando o using, cujo achei o método mais simples e prático.

______________________________________________________________
1. Método utilizando Classe:

using Microsoft.Data.SqlClient;

namespace Cadastro
{

    public class Conexao
    {
        SqlConnection conexao = new SqlConnection();

        public Conexao()
        {
            conexao.ConnectionString = @"Password= colcar senha;Persist Security Info= True;User ID=sa (valor padrão);Database= tabela;Server= nome do Banco de dados que quer acessar";

        }

        public SqlConnection Conectar()
        {
            if (conexao.State == System.Data.ConnectionState.Closed)
            {
                conexao.Open();
            }
            return conexao;
        }

        public void Desconectra()
        {
            if (conexao.State == System.Data.ConnectionState.Open)
            {
                conexao.Close();
            }
           
        }
    }
}

Desta forma criamos o contrutor para sempre se chamado primeiro nessa classe. Em seguida foi criado dois métodos o de conectar e desconectar no qual deverá ser chamado no escopo de main para abrir e fechar a conexão.
Quando você cria uma instância de SqlConnection , todas as propriedades são definidas como seus valores iniciais. Para obter uma lista desses valores, consulte o SqlConnection construtor.

var connection = new SqlConnection();
        connection.Open(); //abrir a conexão

        //INSERT
        //UPDATE

        connection.Close();/*se for fechar conecção e depois utilizar novamente 
                            - OBS: é importante sempre fechar a conexão.*/

        connection.Dispose(); /*quando for fechar uma conexão e não irá abrir mais
         *se vc usar o dispose terá que estanciar o objeto novamente (new), ou seja,
         o connection.open() não funcionará*/

Se o SqlConnection  escopo ficar fora do escopo, ele não será fechado. Portanto, você deve fechar explicitamente a conexão chamando Close ou Dispose. Close e Dispose são funcionalmente equivalentes.

______________________________________________________________
2. Método utilizando using:

Para garantir que as conexões estejam sempre fechadas, abra a conexão dentro de um using bloco, conforme mostrado no fragmento de código a seguir. Isso garante que a conexão seja fechada automaticamente quando o código sair do bloco.

using Microsoft.Data.SqlClient;

namespace BaltaDataAccesse;

public class Program
{
    public static void Main(string[] args)
    {
        const string connectionString = "Server=localhost...";//string de conecção
        
        using (var connection = new SqlConnection(connectionString))
        {
            Console.WriteLine("Conectado");
        }
        
    }
}

Podemos verificar que foi criado uma constante para interligar a connection string, assim não precisamos criar uma classe para a conexão, em seguida foi utilizado o using concatenando com a constante criada, desta forma não precisamos ficar abrindo e fechando a conexão, ou seja, utilizar o connection.Open(), connection.Close() e connection.Dispose() para a utilização do CRUD.

Segui o link como conseguir de froma mais rapida a string connection:  https://www.youtube.com/watch?v=yvNW8oEKQ7Q
