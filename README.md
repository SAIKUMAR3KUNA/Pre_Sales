import { useState, useRef, useEffect } from "react";
import { Input } from "@/components/ui/input";
import { Button } from "@/components/ui/button";
import { Textarea } from "@/components/ui/textarea";
import { Mic, CheckCircle, Trash2, Camera, Fingerprint } from "lucide-react";
import SignatureCanvas from "react-signature-canvas";

export default function UserForm() {
  const [firstName, setFirstName] = useState("");
  const [lastName, setLastName] = useState("");
  const [email, setEmail] = useState("");
  const [phone, setPhone] = useState("");
  const [emailOTP, setEmailOTP] = useState("");
  const [phoneOTP, setPhoneOTP] = useState("");
  const [isEmailVerified, setIsEmailVerified] = useState(false);
  const [isPhoneVerified, setIsPhoneVerified] = useState(false);
  const [emailOTPRequested, setEmailOTPRequested] = useState(false);
  const [phoneOTPRequested, setPhoneOTPRequested] = useState(false);
  const [notes, setNotes] = useState("");
  const [attachment, setAttachment] = useState(null);
  const [fingerprintCaptured, setFingerprintCaptured] = useState(false);
  const signatureRef = useRef(null);
  
  const sendEmailOTP = () => {
    setEmailOTPRequested(true);
    alert("OTP sent to email");
  };

  const sendPhoneOTP = () => {
    setPhoneOTPRequested(true);
    alert("OTP sent to phone");
  };

  const verifyEmailOTP = () => {
    if (emailOTP === "123456") {
      setIsEmailVerified(true);
      alert("Email verified successfully");
    } else {
      alert("Invalid email OTP");
    }
  };

  const verifyPhoneOTP = () => {
    if (phoneOTP === "123456") {
      setIsPhoneVerified(true);
      alert("Phone verified successfully");
    } else {
      alert("Invalid phone OTP");
    }
  };

  const clearSignature = () => signatureRef.current?.clear();

  const handleFileUpload = (event) => {
    setAttachment(event.target.files[0]);
  };

  const handleCapture = async () => {
    try {
      const stream = await navigator.mediaDevices.getUserMedia({ video: true });
      const track = stream.getVideoTracks()[0];
      const imageCapture = new ImageCapture(track);
      const blob = await imageCapture.takePhoto().catch(() => null);
      if (blob) {
        setAttachment(blob);
      } else {
        alert("Unable to capture photo. Please try again.");
      }
      track.stop();
    } catch (error) {
      console.error("Camera access error: ", error);
      alert("Camera access denied. Please grant camera permissions in your browser settings.");
    }
  };

  const captureFingerprint = () => {
    alert("Fingerprint captured successfully");
    setFingerprintCaptured(true);
  };

  return (
    <div className="max-w-lg mx-auto p-6 bg-white shadow rounded-xl space-y-4">
      <h2 className="text-xl font-bold">User Registration</h2>
      <Input placeholder="First Name" value={firstName} onChange={(e) => setFirstName(e.target.value)} />
      <Input placeholder="Last Name" value={lastName} onChange={(e) => setLastName(e.target.value)} />
      <div className="flex space-x-2">
        <Input placeholder="Email" type="email" value={email} onChange={(e) => setEmail(e.target.value)} />
        <Button onClick={sendEmailOTP}>Verify</Button>
      </div>
      {emailOTPRequested && (
        <div className="flex space-x-2">
          <Input placeholder="Enter Email OTP" value={emailOTP} onChange={(e) => setEmailOTP(e.target.value)} />
          <Button onClick={verifyEmailOTP}>Verify OTP</Button>
        </div>
      )}
      <div className="flex space-x-2">
        <Input placeholder="Phone Number" type="tel" value={phone} onChange={(e) => setPhone(e.target.value)} />
        <Button onClick={sendPhoneOTP}>Verify</Button>
      </div>
      {phoneOTPRequested && (
        <div className="flex space-x-2">
          <Input placeholder="Enter Phone OTP" value={phoneOTP} onChange={(e) => setPhoneOTP(e.target.value)} />
          <Button onClick={verifyPhoneOTP}>Verify OTP</Button>
        </div>
      )}
      <label className="block text-sm font-medium">Attach Document</label>
      <Input type="file" onChange={handleFileUpload} />
      <div className="flex space-x-4 mt-2">
        <Button onClick={handleCapture}><Camera /> Capture</Button>
        <Button onClick={captureFingerprint}><Fingerprint /> Capture Fingerprint</Button>
      </div>
      <label className="block text-sm font-medium mt-4">Signature</label>
      <div className="border p-2 rounded-md bg-gray-100">
        <SignatureCanvas ref={signatureRef} penColor="black" canvasProps={{ width: 400, height: 150, className: "sigCanvas" }} />
      </div>
      <Button onClick={clearSignature} variant="outline" className="mt-2 flex items-center"><Trash2 className="mr-2" /> Clear Signature</Button>
      <Button className="w-full mt-4">Submit</Button>
    </div>
  );
}
