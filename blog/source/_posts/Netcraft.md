# Netcraft



已剪辑自: [https://www.yiibai.com/ethical_hacking/netcraft.html](https://www.yiibai.com/ethical_hacking/netcraft.html) 在本节中，我们将学习如何获取有关目标网站使用的技术的信息。为此，我们将使用一个名为Netcraft(https://www.netcraft.com )的网站，然后我们将目标地址放入，这里选择的目标域名地址是isecur1ty.org，然后单击箭头 如下屏幕截图所示：

![Netcraft%20ab4a05f53f304c50947a9b434d929f1e/image1.png](http://cdn.mxrblog.cn/image1.png)

在此之后，单击Site Report，如以下屏幕截图所示：

![Netcraft%20ab4a05f53f304c50947a9b434d929f1e/image2.png](http://cdn.mxrblog.cn/image2.png)

在上面屏幕截图中，我们可以看到一些基本信息，如网站标题，网站排名，描述，关键字以及网站创建时间：

![Netcraft%20ab4a05f53f304c50947a9b434d929f1e/image3.png](http://cdn.mxrblog.cn/image3.png)

当向下滚动时，可以看到网站本身，域名，IP地址和域名注册商，域名注册商是为isecur1ty注册域名的公司：

![Netcraft%20ab4a05f53f304c50947a9b434d929f1e/image4.png](http://cdn.mxrblog.cn/image4.png)

在上面的屏幕截图中，通常会看到有关组织的信息，但在这里不能看到，这是因为isecur1ty使用隐私保护。通常，我们应该能够看到这些信息甚至更多。 在前面的截图中，我们可以看到它在英国托管，也可以看到名称服务器，它是ns1.digitalocean.com，如果我们打开ns1.digitalocean.com，就会发现这是一个网站托管的网站。 现在，我们知道这是一个Web托管公司，在最坏的情况下，可以使用它或尝试入侵ns1.digitalocean.com本身以获得对isecur1ty的访问权限。 如果进一步向下滚动，我们将看到使用的托管公司的托管历史。可以看到最新版本在Linux上运行Apache，与在上一节中看到的服务器相同，2.2.31使用Unix mod_ssl以及所有其他附加组件：

![Netcraft%20ab4a05f53f304c50947a9b434d929f1e/image5.png](http://cdn.mxrblog.cn/image5.png)

同样，它对于在目标计算机上查找漏洞和漏洞非常重要。 向下滚动到Web Track ers，它将向我们展示目标上使用的第三方应用程序，因此我们可以看到目标使用MaxCDN，Google和其他Google服务。这也可以帮助我们找到并获得对目标计算机的访问权限，如以下屏幕截图所示：

![Netcraft%20ab4a05f53f304c50947a9b434d929f1e/image6.png](http://cdn.mxrblog.cn/image6.png)

“Technology”选项卡显示了目标网站上使用的技术：

![Netcraft%20ab4a05f53f304c50947a9b434d929f1e/image7.png](http://cdn.mxrblog.cn/image7.png)

在上面的屏幕截图中，可以看到它使用Apache Web服务器。在服务器端，可以看到网站使用PHP，这意味着网站可以理解并运行PHP代码。将来，如果我们设法在目标上运行任何类型的代码，那么代码应该作为PHP代码发送。要在Metasploit或Veil-Evasion上创建有效负载，我们应该以PHP格式创建它们，并且目标网站将能够运行它们，因为它支持PHP。 在客户端，可以在前面的截图中看到该网站支持JavaScript，因此如果在网站上运行JavaScript，它将不会在网站服务器上执行，它将在用户端执行，因为JavaScript是客户端语言而PHP是服务器端语言。如果设法运行PHP代码，它将在服务器本身上执行。如果我们设法运行JavaScript，它将在用户端上执行。它和jQuery一样。这只是JavaScript的框架。 在下面的屏幕截图中，向下滚动，该网站使用WordPress自托管软件。Netcraft将显示网站上使用的任何Web应用程序：

![Netcraft%20ab4a05f53f304c50947a9b434d929f1e/image8.png](http://cdn.mxrblog.cn/image8.png)

WordPress只是一个Web应用程序，因此我们可以在这个案例中看到其他示例，它是一个开源Web应用程序，还有很多其他网站可能有。如果我们足够幸运地找到现有的，那么我们可以继续在目标网站上利用它。例如，假设我们有WordPress，如果访问https://www.exploit-db.com/并搜索WordPress，我们将能够找到许多与WordPress相关的漏洞。 有不同版本的WordPress。我们需要确保目标版本相同。我们下面来看一个示例来了解如何使用漏洞，但它只是展示了信息收集的强大功能。如果我们进一步滚动，我们会发现其他信息，如网站使用HTML5和CSS，以及所有类型的东西，如以下屏幕截图所示：

![Netcraft%20ab4a05f53f304c50947a9b434d929f1e/image9.png](http://cdn.mxrblog.cn/image9.png)

Netcraft主要用于了解网站。我们收集了有关它在PHP上运行的站点的信息，并运行JavaScript。它使用WordPress，因此我们可以使用WordPress入侵网站。如果向上滚动，还发现了网站的虚拟主机。因此，在最不理想的情况下，我们可以尝试入侵网络托管服务器并访问我们的目标网站。