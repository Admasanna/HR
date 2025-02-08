import React, { useState, useEffect } from "react";
import axios from "axios";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Textarea } from "@/components/ui/textarea";
import { Card, CardContent } from "@/components/ui/card";
import { Loader } from "lucide-react";
import { Table } from "@/components/ui/table";
import { Auth } from 'aws-amplify';
import { saveAs } from 'file-saver';
import StripeCheckout from "react-stripe-checkout";

export default function JobMatchingApp() {
  const [jobDescription, setJobDescription] = useState("");
  const [resumes, setResumes] = useState([]);
  const [results, setResults] = useState(null);
  const [loading, setLoading] = useState(false);
  const [authenticated, setAuthenticated] = useState(false);
  const [registering, setRegistering] = useState(false);
  const [paymentCompleted, setPaymentCompleted] = useState(false);
  const [dashboardData, setDashboardData] = useState(null);
  const [interviewQuestions, setInterviewQuestions] = useState([]);
  const [jobPosts, setJobPosts] = useState([]);
  const [notifications, setNotifications] = useState([]);
  const [chatMessages, setChatMessages] = useState([]);

  useEffect(() => {
    if (authenticated) {
      fetchDashboardData();
      fetchNotifications();
    }
  }, [authenticated]);

  const fetchDashboardData = async () => {
    try {
      const token = await Auth.currentSession();
      const response = await axios.get("https://your-api-endpoint.com/dashboard", {
        headers: { Authorization: `Bearer ${token.getIdToken().getJwtToken()}` }
      });
      setDashboardData(response.data);
    } catch (error) {
      console.error("Error fetching dashboard data:", error);
    }
  };

  const fetchNotifications = async () => {
    try {
      const response = await axios.get("https://your-api-endpoint.com/notifications");
      setNotifications(response.data);
    } catch (error) {
      console.error("Error fetching notifications:", error);
    }
  };

  const postJob = async () => {
    try {
      const token = await Auth.currentSession();
      const response = await axios.post("https://your-api-endpoint.com/post-job", {
        job_description: jobDescription,
      }, {
        headers: { Authorization: `Bearer ${token.getIdToken().getJwtToken()}` }
      });
      setJobPosts([...jobPosts, response.data]);
      setJobDescription("");
    } catch (error) {
      console.error("Error posting job:", error);
    }
  };

  const analyzeResumes = async () => {
    setLoading(true);
    try {
      const token = await Auth.currentSession();
      const response = await axios.post("https://your-api-endpoint.com/analyze", {
        job_description: jobDescription,
        resumes: resumes,
      }, {
        headers: { Authorization: `Bearer ${token.getIdToken().getJwtToken()}` }
      });
      setResults(response.data);
    } catch (error) {
      console.error("Error:", error);
    }
    setLoading(false);
  };

  const generateInterviewQuestions = async () => {
    try {
      const response = await axios.post("https://your-api-endpoint.com/generate-questions", {
        job_description: jobDescription,
      });
      setInterviewQuestions(response.data.questions);
    } catch (error) {
      console.error("Error generating interview questions:", error);
    }
  };

  const handlePayment = async (token) => {
    try {
      await axios.post("https://your-api-endpoint.com/charge", { token });
      setPaymentCompleted(true);
    } catch (error) {
      console.error("Payment failed", error);
    }
  };

  const sendMessageToChatbot = async (message) => {
    try {
      const response = await axios.post("https://your-api-endpoint.com/chatbot", { message });
      setChatMessages([...chatMessages, { user: "HR", text: message }, { user: "AI", text: response.data.reply }]);
    } catch (error) {
      console.error("Error sending message to chatbot:", error);
    }
  };

  return (
    <div className="max-w-5xl mx-auto p-4">
      <h1 className="text-2xl font-bold mb-4 text-center">AI-Powered HR Dashboard</h1>
      {!authenticated ? (
        <div>
          {registering ? (
            <div>
              <h2 className="text-lg font-semibold">Employer Registration</h2>
              <Input placeholder="Email" className="mb-2" />
              <Input type="password" placeholder="Password" className="mb-2" />
              <h3 className="text-md font-semibold">Complete Payment</h3>
              <StripeCheckout
                stripeKey="your-stripe-public-key"
                token={handlePayment}
                amount={4999}
                name="AI Recruitment Subscription"
                currency="USD"
              />
              {paymentCompleted && <p className="text-green-500">Payment Successful! You can now log in.</p>}
              <Button onClick={() => setRegistering(false)} className="ml-2">Back to Login</Button>
            </div>
          ) : (
            <div>
              <h2 className="text-lg font-semibold">HR Login</h2>
              <Input placeholder="Email" className="mb-2" />
              <Input type="password" placeholder="Password" className="mb-2" />
              <Button onClick={() => setAuthenticated(true)}>Sign In</Button>
              <Button onClick={() => setRegistering(true)} className="ml-2">Register</Button>
            </div>
          )}
        </div>
      ) : (
        <div>
          <Card>
            <CardContent>
              <h2 className="text-xl font-semibold mb-2">Notifications</h2>
              <ul>
                {notifications.map((note, index) => (
                  <li key={index}>{note.message}</li>
                ))}
              </ul>
              <h2 className="text-xl font-semibold mt-4">AI Chatbot</h2>
              <Input placeholder="Ask AI a question..." onKeyDown={(e) => e.key === 'Enter' && sendMessageToChatbot(e.target.value)} />
              <ul>
                {chatMessages.map((msg, index) => (
                  <li key={index}><strong>{msg.user}:</strong> {msg.text}</li>
                ))}
              </ul>
            </CardContent>
          </Card>
        </div>
      )}
    </div>
  );
}
