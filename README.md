# Magister
Magister
    queryKey: ["/api/admin/stats"],
  });
  const { data: attempts, isLoading: attemptsLoading } = useQuery<TrainingAttempt[]>({
    queryKey: ["/api/admin/attempts"],
  });
  const exportData = () => {
    if (!attempts) return;
    
    const csvContent = [
      ["Gebruikersnaam", "Tijdstip", "IP Adres", "User Agent", "Training Voltooid"].join(","),
      ...attempts.map(attempt => [
        attempt.username,
        format(new Date(attempt.timestamp), "yyyy-MM-dd HH:mm:ss"),
        attempt.ipAddress || "Onbekend",
        attempt.userAgent || "Onbekend",
        attempt.completedEducation ? "Ja" : "Nee"
      ].join(","))
    ].join("\n");
    const blob = new Blob([csvContent], { type: "text/csv;charset=utf-8;" });
    const link = document.createElement("a");
    const url = URL.createObjectURL(blob);
    link.setAttribute("href", url);
    link.setAttribute("download", `phishing-training-${format(new Date(), "yyyy-MM-dd")}.csv`);
    link.style.visibility = "hidden";
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
  };
  if (statsLoading || attemptsLoading) {
    return (
      <div className="min-h-screen bg-gray-50 p-6">
        <div className="max-w-7xl mx-auto">
          <div className="animate-pulse">
            <div className="h-8 bg-gray-200 rounded w-1/4 mb-4"></div>
            <div className="grid grid-cols-1 md:grid-cols-4 gap-6 mb-8">
              {[...Array(4)].map((_, i) => (
                <div key={i} className="bg-white rounded-lg shadow-sm p-6 border border-gray-200">
                  <div className="h-4 bg-gray-200 rounded w-3/4 mb-2"></div>
                  <div className="h-8 bg-gray-200 rounded w-1/2"></div>
                </div>
              ))}
            </div>
          </div>
        </div>
      </div>
    );
  }
  const phishedUsers = attempts?.length || 0;
  const completedEducation = attempts?.filter(a => a.completedEducation).length || 0;
  const successRate = phishedUsers > 0 ? Math.round((completedEducation / phishedUsers) * 100) : 0;
  return (
    <div className="min-h-screen bg-gray-50 p-6">
      <div className="max-w-7xl mx-auto">
        <div className="mb-8">
          <h1 className="text-2xl font-bold text-gray-900 mb-2">Training Dashboard</h1>
          <p className="text-gray-600">Overzicht van phishing training sessies en resultaten</p>
        </div>
        {/* Stats Overview */}
        <div className="grid grid-cols-1 md:grid-cols-4 gap-6 mb-8">
          <Card className="border border-gray-200">
            <CardContent className="p-6">
              <div className="flex items-center">
                <div className="flex-shrink-0">
                  <div className="w-8 h-8 bg-blue-100 rounded-lg flex items-center justify-center">
                    <Users className="w-4 h-4 text-blue-600" />
                  </div>
                </div>
                <div className="ml-4">
                  <p className="text-sm font-medium text-gray-600">Unieke Gebruikers</p>
                  <p className="text-2xl font-bold text-gray-900">{stats?.uniqueUsers || 0}</p>
                </div>
              </div>
            </CardContent>
          </Card>
          <Card className="border border-gray-200">
            <CardContent className="p-6">
              <div className="flex items-center">
                <div className="flex-shrink-0">
                  <div className="w-8 h-8 bg-red-100 rounded-lg flex items-center justify-center">
                    <AlertTriangle className="w-4 h-4 text-red-600" />
                  </div>
                </div>
                <div className="ml-4">
                  <p className="text-sm font-medium text-gray-600">Phishing Pogingen</p>
                  <p className="text-2xl font-bold text-gray-900">{phishedUsers}</p>
                </div>
              </div>
            </CardContent>
          </Card>
          <Card className="border border-gray-200">
            <CardContent className="p-6">
              <div className="flex items-center">
                <div className="flex-shrink-0">
                  <div className="w-8 h-8 bg-green-100 rounded-lg flex items-center justify-center">
                    <Shield className="w-4 h-4 text-green-600" />
                  </div>
                </div>
                <div className="ml-4">
                  <p className="text-sm font-medium text-gray-600">Training Voltooid</p>
                  <p className="text-2xl font-bold text-gray-900">{completedEducation}</p>
                </div>
              </div>
            </CardContent>
          </Card>
          <Card className="border border-gray-200">
            <CardContent className="p-6">
              <div className="flex items-center">
                <div className="flex-shrink-0">
                  <div className="w-8 h-8 bg-yellow-100 rounded-lg flex items-center justify-center">
                    <Percent className="w-4 h-4 text-yellow-600" />
                  </div>
                </div>
                <div className="ml-4">
                  <p className="text-sm font-medium text-gray-600">Training Percent</p>
                  <p className="text-2xl font-bold text-gray-900">{successRate}%</p>
                </div>
              </div>
            </CardContent>
          </Card>
        </div>
        {/* Training Sessions Table */}
        <Card className="border border-gray-200">
          <CardHeader className="px-6 py-4 border-b border-gray-200">
            <div className="flex items-center justify-between">
              <CardTitle className="text-lg font-medium text-gray-900">Training Sessies</CardTitle>
              <div className="flex items-center space-x-3">
                <Button 
                  onClick={exportData}
                  className="bg-green-600 text-white hover:bg-green-700 transition-colors"
                  size="sm"
                >
                  <Download className="w-4 h-4 mr-2" />
                  Exporteren
                </Button>
              </div>
            </div>
          </CardHeader>
          <CardContent className="p-0">
            <div className="overflow-x-auto">
              <Table>
                <TableHeader>
                  <TableRow>
                    <TableHead className="px-6 py-3">Gebruiker</TableHead>
                    <TableHead className="px-6 py-3">Tijdstip</TableHead>
                    <TableHead className="px-6 py-3">Status</TableHead>
                    <TableHead className="px-6 py-3">IP Adres</TableHead>
                    <TableHead className="px-6 py-3">Training Voltooid</TableHead>
                    <TableHead className="px-6 py-3">User Agent</TableHead>
                  </TableRow>
                </TableHeader>
                <TableBody>
                  {attempts?.map((attempt) => (
                    <TableRow key={attempt.id}>
                      <TableCell className="px-6 py-4">
                        <div className="flex items-center">
                          <div className="flex-shrink-0 h-10 w-10">
                            <div className="h-10 w-10 rounded-full bg-gray-300 flex items-center justify-center">
                              <span className="text-gray-600 font-medium text-sm">
                                {attempt.username.substring(0, 2).toUpperCase()}
                              </span>
                            </div>
                          </div>
                          <div className="ml-4">
                            <div className="text-sm font-medium text-gray-900">{attempt.username}</div>
                          </div>
                        </div>
                      </TableCell>
                      <TableCell className="px-6 py-4 text-sm text-gray-900">
                        {format(new Date(attempt.timestamp), "dd MMM yyyy HH:mm", { locale: nl })}
                      </TableCell>
                      <TableCell className="px-6 py-4">
                        <Badge variant="destructive">
                          <AlertTriangle className="w-3 h-3 mr-1" />
                          Gevallen voor phishing
                        </Badge>
                      </TableCell>
                      <TableCell className="px-6 py-4 text-sm text-gray-900">
                        {attempt.ipAddress || "Onbekend"}
                      </TableCell>
                      <TableCell className="px-6 py-4">
                        {attempt.completedEducation ? (
                          <Badge variant="secondary" className="bg-green-100 text-green-800">
                            <Shield className="w-3 h-3 mr-1" />
                            Ja
                          </Badge>
                        ) : (
                          <Badge variant="outline">
                            Nee
                          </Badge>
                        )}
                      </TableCell>
                      <TableCell className="px-6 py-4 text-sm text-gray-500 max-w-xs truncate">
                        {attempt.userAgent || "Onbekend"}
                      </TableCell>
                    </TableRow>
                  ))}
                  {!attempts?.length && (
                    <TableRow>
                      <TableCell colSpan={6} className="px-6 py-8 text-center text-gray-500">
                        Nog geen training pogingen geregistreerd.
                      </TableCell>
                    </TableRow>
                  )}
                </TableBody>
              </Table>
            </div>
          </CardContent>
        </Card>
      </div>
    </div>
  );
}
client/src/components/educational-modal.tsx
import { Dialog, DialogContent, DialogHeader, DialogTitle } from "@/components/ui/dialog";
import { Button } from "@/components/ui/button";
import { AlertTriangle, Info, CheckCircle, GraduationCap, X } from "lucide-react";
interface EducationalModalProps {
  isOpen: boolean;
  onClose: () => void;
  onEducationComplete: () => void;
}
export default function EducationalModal({ isOpen, onClose, onEducationComplete }: EducationalModalProps) {
  return (
    <Dialog open={isOpen} onOpenChange={onClose}>
      <DialogContent className="max-w-2xl">
        <DialogHeader>
          <div className="text-center mb-6">
            <div className="mx-auto flex items-center justify-center h-16 w-16 rounded-full bg-yellow-100 mb-4">
              <AlertTriangle className="text-yellow-600 w-8 h-8" />
            </div>
            <DialogTitle className="text-2xl font-bold text-gray-900 mb-2">
              Oeps! Je bent gevallen voor een phishing simulatie
            </DialogTitle>
            <p className="text-gray-600">Maar geen zorgen - dit is een veilige leeromgeving!</p>
          </div>
        </DialogHeader>
        <div className="space-y-6">
          <div className="bg-red-50 border-l-4 border-red-400 p-4">
            <div className="flex">
              <div className="flex-shrink-0">
                <Info className="text-red-400 w-5 h-5" />
              </div>
              <div className="ml-3">
                <h4 className="text-sm font-medium text-red-800">Wat er gebeurde:</h4>
                <p className="mt-1 text-sm text-red-700">
                  Je hebt zojuist je inloggegevens ingevoerd op een nepversie van Magister. 
                  In een echte phishing aanval zouden je gegevens nu gestolen zijn.
                </p>
              </div>
            </div>
          </div>
          <div className="bg-blue-50 border-l-4 border-blue-400 p-4">
            <div className="flex">
              <div className="flex-shrink-0">
                <Info className="text-blue-400 w-5 h-5" />
              </div>
              <div className="ml-3">
                <h4 className="text-sm font-medium text-blue-800">Waarop je had kunnen letten:</h4>
                <ul className="mt-2 text-sm text-blue-700 space-y-1">
                  <li>• Controleer altijd de URL in je adresbalk</li>
                  <li>• Let op spelfouten en verdachte domeinnamen</li>
                  <li>• Wees voorzichtig met links in e-mails</li>
                  <li>• Gebruik tweefactorauthenticatie waar mogelijk</li>
                  <li>• Verifieer altijd de identiteit van de afzender</li>
                </ul>
              </div>
            </div>
          </div>
          <div className="bg-green-50 border-l-4 border-green-400 p-4">
            <div className="flex">
              <div className="flex-shrink-0">
                <CheckCircle className="text-green-400 w-5 h-5" />
              </div>
              <div className="ml-3">
                <h4 className="text-sm font-medium text-green-800">Je privacy is beschermd:</h4>
                <p className="mt-1 text-sm text-green-700">
                  Je echte wachtwoord is veilig. Deze training gebruikt geen echte inloggegevens 
                  en alle data wordt alleen voor educatieve doeleinden gebruikt.
                </p>
              </div>
            </div>
          </div>
        </div>
        <div className="mt-8 flex flex-col sm:flex-row gap-3">
          <Button 
            onClick={onEducationComplete}
            className="flex-1 bg-blue-600 text-white hover:bg-blue-700 transition-colors"
          >
            <GraduationCap className="w-4 h-4 mr-2" />
            Training Voltooid
          </Button>
          <Button 
            onClick={onClose}
            variant="outline"
            className="flex-1"
          >
            <X className="w-4 h-4 mr-2" />
            Sluiten
          </Button>
        </div>
        <div className="mt-4 text-center text-sm text-gray-500">
          <p>Vragen over deze training? Neem contact op met je IT-afdeling.</p>
        </div>
      </DialogContent>
    </Dialog>
  );
}
client/src/lib/queryClient.ts
import { QueryClient } from "@tanstack/react-query";
async function throwIfResNotOk(res: Response) {
  if (!res.ok) {
    const body = await res.text();
    throw new Error(`${res.status} ${res.statusText}: ${body}`);
  }
}
export async function apiRequest(
  method: "GET" | "POST" | "PUT" | "DELETE" | "PATCH",
  path: string,
  body?: Record<string, any>
): Promise<Response> {
  const requestInit: RequestInit = {
    method,
    headers: {
      "Content-Type": "application/json",
    },
  };
  if (body) {
    requestInit.body = JSON.stringify(body);
  }
  const res = await fetch(path, requestInit);
  await throwIfResNotOk(res);
  return res;
}
type UnauthorizedBehavior = "returnNull" | "throw";
export const getQueryFn: <T>(options: {
  on401: UnauthorizedBehavior;
}) => ({ queryKey }: { queryKey: readonly any[] }) => Promise<T> =
  ({ on401 }) =>
  ({ queryKey }) => {
    const path = queryKey[0] as string;
    return apiRequest("GET", path).then(async (res) => {
      if (res.status === 401) {
        if (on401 === "returnNull") {
          return null;
        } else {
          throw new Error("Unauthorized");
        }
      }
      return res.json();
    });
  };
export const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      queryFn: getQueryFn({ on401: "throw" }),
    },
  },
});    queryKey: ["/api/admin/stats"],
  });
  const { data: attempts, isLoading: attemptsLoading } = useQuery<TrainingAttempt[]>({
    queryKey: ["/api/admin/attempts"],
  });
  const exportData = () => {
    if (!attempts) return;
    
    const csvContent = [
      ["Gebruikersnaam", "Tijdstip", "IP Adres", "User Agent", "Training Voltooid"].join(","),
      ...attempts.map(attempt => [
        attempt.username,
        format(new Date(attempt.timestamp), "yyyy-MM-dd HH:mm:ss"),
        attempt.ipAddress || "Onbekend",
        attempt.userAgent || "Onbekend",
        attempt.completedEducation ? "Ja" : "Nee"
      ].join(","))
    ].join("\n");
    const blob = new Blob([csvContent], { type: "text/csv;charset=utf-8;" });
    const link = document.createElement("a");
    const url = URL.createObjectURL(blob);
    link.setAttribute("href", url);
    link.setAttribute("download", `phishing-training-${format(new Date(), "yyyy-MM-dd")}.csv`);
    link.style.visibility = "hidden";
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
  };
  if (statsLoading || attemptsLoading) {
    return (
      <div className="min-h-screen bg-gray-50 p-6">
        <div className="max-w-7xl mx-auto">
          <div className="animate-pulse">
            <div className="h-8 bg-gray-200 rounded w-1/4 mb-4"></div>
            <div className="grid grid-cols-1 md:grid-cols-4 gap-6 mb-8">
              {[...Array(4)].map((_, i) => (
                <div key={i} className="bg-white rounded-lg shadow-sm p-6 border border-gray-200">
                  <div className="h-4 bg-gray-200 rounded w-3/4 mb-2"></div>
                  <div className="h-8 bg-gray-200 rounded w-1/2"></div>
                </div>
              ))}
            </div>
          </div>
        </div>
      </div>
    );
  }
  const phishedUsers = attempts?.length || 0;
  const completedEducation = attempts?.filter(a => a.completedEducation).length || 0;
  const successRate = phishedUsers > 0 ? Math.round((completedEducation / phishedUsers) * 100) : 0;
  return (
    <div className="min-h-screen bg-gray-50 p-6">
      <div className="max-w-7xl mx-auto">
        <div className="mb-8">
          <h1 className="text-2xl font-bold text-gray-900 mb-2">Training Dashboard</h1>
          <p className="text-gray-600">Overzicht van phishing training sessies en resultaten</p>
        </div>
        {/* Stats Overview */}
        <div className="grid grid-cols-1 md:grid-cols-4 gap-6 mb-8">
          <Card className="border border-gray-200">
            <CardContent className="p-6">
              <div className="flex items-center">
                <div className="flex-shrink-0">
                  <div className="w-8 h-8 bg-blue-100 rounded-lg flex items-center justify-center">
                    <Users className="w-4 h-4 text-blue-600" />
                  </div>
                </div>
                <div className="ml-4">
                  <p className="text-sm font-medium text-gray-600">Unieke Gebruikers</p>
                  <p className="text-2xl font-bold text-gray-900">{stats?.uniqueUsers || 0}</p>
                </div>
              </div>
            </CardContent>
          </Card>
          <Card className="border border-gray-200">
            <CardContent className="p-6">
              <div className="flex items-center">
                <div className="flex-shrink-0">
                  <div className="w-8 h-8 bg-red-100 rounded-lg flex items-center justify-center">
                    <AlertTriangle className="w-4 h-4 text-red-600" />
                  </div>
                </div>
                <div className="ml-4">
                  <p className="text-sm font-medium text-gray-600">Phishing Pogingen</p>
                  <p className="text-2xl font-bold text-gray-900">{phishedUsers}</p>
                </div>
              </div>
            </CardContent>
          </Card>
          <Card className="border border-gray-200">
            <CardContent className="p-6">
              <div className="flex items-center">
                <div className="flex-shrink-0">
                  <div className="w-8 h-8 bg-green-100 rounded-lg flex items-center justify-center">
                    <Shield className="w-4 h-4 text-green-600" />
                  </div>
                </div>
                <div className="ml-4">
                  <p className="text-sm font-medium text-gray-600">Training Voltooid</p>
                  <p className="text-2xl font-bold text-gray-900">{completedEducation}</p>
                </div>
              </div>
            </CardContent>
          </Card>
          <Card className="border border-gray-200">
            <CardContent className="p-6">
              <div className="flex items-center">
                <div className="flex-shrink-0">
                  <div className="w-8 h-8 bg-yellow-100 rounded-lg flex items-center justify-center">
                    <Percent className="w-4 h-4 text-yellow-600" />
                  </div>
                </div>
                <div className="ml-4">
                  <p className="text-sm font-medium text-gray-600">Training Percent</p>
                  <p className="text-2xl font-bold text-gray-900">{successRate}%</p>
                </div>
              </div>
            </CardContent>
          </Card>
        </div>
        {/* Training Sessions Table */}
        <Card className="border border-gray-200">
          <CardHeader className="px-6 py-4 border-b border-gray-200">
            <div className="flex items-center justify-between">
              <CardTitle className="text-lg font-medium text-gray-900">Training Sessies</CardTitle>
              <div className="flex items-center space-x-3">
                <Button 
                  onClick={exportData}
                  className="bg-green-600 text-white hover:bg-green-700 transition-colors"
                  size="sm"
                >
                  <Download className="w-4 h-4 mr-2" />
                  Exporteren
                </Button>
              </div>
            </div>
          </CardHeader>
          <CardContent className="p-0">
            <div className="overflow-x-auto">
              <Table>
                <TableHeader>
                  <TableRow>
                    <TableHead className="px-6 py-3">Gebruiker</TableHead>
                    <TableHead className="px-6 py-3">Tijdstip</TableHead>
                    <TableHead className="px-6 py-3">Status</TableHead>
                    <TableHead className="px-6 py-3">IP Adres</TableHead>
                    <TableHead className="px-6 py-3">Training Voltooid</TableHead>
                    <TableHead className="px-6 py-3">User Agent</TableHead>
                  </TableRow>
                </TableHeader>
                <TableBody>
                  {attempts?.map((attempt) => (
                    <TableRow key={attempt.id}>
                      <TableCell className="px-6 py-4">
                        <div className="flex items-center">
                          <div className="flex-shrink-0 h-10 w-10">
                            <div className="h-10 w-10 rounded-full bg-gray-300 flex items-center justify-center">
                              <span className="text-gray-600 font-medium text-sm">
                                {attempt.username.substring(0, 2).toUpperCase()}
                              </span>
                            </div>
                          </div>
                          <div className="ml-4">
                            <div className="text-sm font-medium text-gray-900">{attempt.username}</div>
                          </div>
                        </div>
                      </TableCell>
                      <TableCell className="px-6 py-4 text-sm text-gray-900">
                        {format(new Date(attempt.timestamp), "dd MMM yyyy HH:mm", { locale: nl })}
                      </TableCell>
                      <TableCell className="px-6 py-4">
                        <Badge variant="destructive">
                          <AlertTriangle className="w-3 h-3 mr-1" />
                          Gevallen voor phishing
                        </Badge>
                      </TableCell>
                      <TableCell className="px-6 py-4 text-sm text-gray-900">
                        {attempt.ipAddress || "Onbekend"}
                      </TableCell>
                      <TableCell className="px-6 py-4">
                        {attempt.completedEducation ? (
                          <Badge variant="secondary" className="bg-green-100 text-green-800">
                            <Shield className="w-3 h-3 mr-1" />
                            Ja
                          </Badge>
                        ) : (
                          <Badge variant="outline">
                            Nee
                          </Badge>
                        )}
                      </TableCell>
                      <TableCell className="px-6 py-4 text-sm text-gray-500 max-w-xs truncate">
                        {attempt.userAgent || "Onbekend"}
                      </TableCell>
                    </TableRow>
                  ))}
                  {!attempts?.length && (
                    <TableRow>
                      <TableCell colSpan={6} className="px-6 py-8 text-center text-gray-500">
                        Nog geen training pogingen geregistreerd.
                      </TableCell>
                    </TableRow>
                  )}
                </TableBody>
              </Table>
            </div>
          </CardContent>
        </Card>
      </div>
    </div>
  );
}
client/src/components/educational-modal.tsx
import { Dialog, DialogContent, DialogHeader, DialogTitle } from "@/components/ui/dialog";
import { Button } from "@/components/ui/button";
import { AlertTriangle, Info, CheckCircle, GraduationCap, X } from "lucide-react";
interface EducationalModalProps {
  isOpen: boolean;
  onClose: () => void;
  onEducationComplete: () => void;
}
export default function EducationalModal({ isOpen, onClose, onEducationComplete }: EducationalModalProps) {
  return (
    <Dialog open={isOpen} onOpenChange={onClose}>
      <DialogContent className="max-w-2xl">
        <DialogHeader>
          <div className="text-center mb-6">
            <div className="mx-auto flex items-center justify-center h-16 w-16 rounded-full bg-yellow-100 mb-4">
              <AlertTriangle className="text-yellow-600 w-8 h-8" />
            </div>
            <DialogTitle className="text-2xl font-bold text-gray-900 mb-2">
              Oeps! Je bent gevallen voor een phishing simulatie
            </DialogTitle>
            <p className="text-gray-600">Maar geen zorgen - dit is een veilige leeromgeving!</p>
          </div>
        </DialogHeader>
        <div className="space-y-6">
          <div className="bg-red-50 border-l-4 border-red-400 p-4">
            <div className="flex">
              <div className="flex-shrink-0">
                <Info className="text-red-400 w-5 h-5" />
              </div>
              <div className="ml-3">
                <h4 className="text-sm font-medium text-red-800">Wat er gebeurde:</h4>
                <p className="mt-1 text-sm text-red-700">
                  Je hebt zojuist je inloggegevens ingevoerd op een nepversie van Magister. 
                  In een echte phishing aanval zouden je gegevens nu gestolen zijn.
                </p>
              </div>
            </div>
          </div>
          <div className="bg-blue-50 border-l-4 border-blue-400 p-4">
            <div className="flex">
              <div className="flex-shrink-0">
                <Info className="text-blue-400 w-5 h-5" />
              </div>
              <div className="ml-3">
                <h4 className="text-sm font-medium text-blue-800">Waarop je had kunnen letten:</h4>
                <ul className="mt-2 text-sm text-blue-700 space-y-1">
                  <li>• Controleer altijd de URL in je adresbalk</li>
                  <li>• Let op spelfouten en verdachte domeinnamen</li>
                  <li>• Wees voorzichtig met links in e-mails</li>
                  <li>• Gebruik tweefactorauthenticatie waar mogelijk</li>
                  <li>• Verifieer altijd de identiteit van de afzender</li>
                </ul>
              </div>
            </div>
          </div>
          <div className="bg-green-50 border-l-4 border-green-400 p-4">
            <div className="flex">
              <div className="flex-shrink-0">
                <CheckCircle className="text-green-400 w-5 h-5" />
              </div>
              <div className="ml-3">
                <h4 className="text-sm font-medium text-green-800">Je privacy is beschermd:</h4>
                <p className="mt-1 text-sm text-green-700">
                  Je echte wachtwoord is veilig. Deze training gebruikt geen echte inloggegevens 
                  en alle data wordt alleen voor educatieve doeleinden gebruikt.
                </p>
              </div>
            </div>
          </div>
        </div>
        <div className="mt-8 flex flex-col sm:flex-row gap-3">
          <Button 
            onClick={onEducationComplete}
            className="flex-1 bg-blue-600 text-white hover:bg-blue-700 transition-colors"
          >
            <GraduationCap className="w-4 h-4 mr-2" />
            Training Voltooid
          </Button>
          <Button 
            onClick={onClose}
            variant="outline"
            className="flex-1"
          >
            <X className="w-4 h-4 mr-2" />
            Sluiten
          </Button>
        </div>
        <div className="mt-4 text-center text-sm text-gray-500">
          <p>Vragen over deze training? Neem contact op met je IT-afdeling.</p>
        </div>
      </DialogContent>
    </Dialog>
  );
}
client/src/lib/queryClient.ts
import { QueryClient } from "@tanstack/react-query";
async function throwIfResNotOk(res: Response) {
  if (!res.ok) {
    const body = await res.text();
    throw new Error(`${res.status} ${res.statusText}: ${body}`);
  }
}
export async function apiRequest(
  method: "GET" | "POST" | "PUT" | "DELETE" | "PATCH",
  path: string,
  body?: Record<string, any>
): Promise<Response> {
  const requestInit: RequestInit = {
    method,
    headers: {
      "Content-Type": "application/json",
    },
  };
  if (body) {
    requestInit.body = JSON.stringify(body);
  }
  const res = await fetch(path, requestInit);
  await throwIfResNotOk(res);
  return res;
}
type UnauthorizedBehavior = "returnNull" | "throw";
export const getQueryFn: <T>(options: {
  on401: UnauthorizedBehavior;
}) => ({ queryKey }: { queryKey: readonly any[] }) => Promise<T> =
  ({ on401 }) =>
  ({ queryKey }) => {
    const path = queryKey[0] as string;
    return apiRequest("GET", path).then(async (res) => {
      if (res.status === 401) {
        if (on401 === "returnNull") {
          return null;
        } else {
          throw new Error("Unauthorized");
        }
      }
      return res.json();
    });
  };
export const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      queryFn: getQueryFn({ on401: "throw" }),
    },
  },
});    queryKey: ["/api/admin/stats"],
  });
  const { data: attempts, isLoading: attemptsLoading } = useQuery<TrainingAttempt[]>({
    queryKey: ["/api/admin/attempts"],
  });
  const exportData = () => {
    if (!attempts) return;
    
    const csvContent = [
      ["Gebruikersnaam", "Tijdstip", "IP Adres", "User Agent", "Training Voltooid"].join(","),
      ...attempts.map(attempt => [
        attempt.username,
        format(new Date(attempt.timestamp), "yyyy-MM-dd HH:mm:ss"),
        attempt.ipAddress || "Onbekend",
        attempt.userAgent || "Onbekend",
        attempt.completedEducation ? "Ja" : "Nee"
      ].join(","))
    ].join("\n");
    const blob = new Blob([csvContent], { type: "text/csv;charset=utf-8;" });
    const link = document.createElement("a");
    const url = URL.createObjectURL(blob);
    link.setAttribute("href", url);
    link.setAttribute("download", `phishing-training-${format(new Date(), "yyyy-MM-dd")}.csv`);
    link.style.visibility = "hidden";
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
  };
  if (statsLoading || attemptsLoading) {
    return (
      <div className="min-h-screen bg-gray-50 p-6">
        <div className="max-w-7xl mx-auto">
          <div className="animate-pulse">
            <div className="h-8 bg-gray-200 rounded w-1/4 mb-4"></div>
            <div className="grid grid-cols-1 md:grid-cols-4 gap-6 mb-8">
              {[...Array(4)].map((_, i) => (
                <div key={i} className="bg-white rounded-lg shadow-sm p-6 border border-gray-200">
                  <div className="h-4 bg-gray-200 rounded w-3/4 mb-2"></div>
                  <div className="h-8 bg-gray-200 rounded w-1/2"></div>
                </div>
              ))}
            </div>
          </div>
        </div>
      </div>
    );
  }
  const phishedUsers = attempts?.length || 0;
  const completedEducation = attempts?.filter(a => a.completedEducation).length || 0;
  const successRate = phishedUsers > 0 ? Math.round((completedEducation / phishedUsers) * 100) : 0;
  return (
    <div className="min-h-screen bg-gray-50 p-6">
      <div className="max-w-7xl mx-auto">
        <div className="mb-8">
          <h1 className="text-2xl font-bold text-gray-900 mb-2">Training Dashboard</h1>
          <p className="text-gray-600">Overzicht van phishing training sessies en resultaten</p>
        </div>
        {/* Stats Overview */}
        <div className="grid grid-cols-1 md:grid-cols-4 gap-6 mb-8">
          <Card className="border border-gray-200">
            <CardContent className="p-6">
              <div className="flex items-center">
                <div className="flex-shrink-0">
                  <div className="w-8 h-8 bg-blue-100 rounded-lg flex items-center justify-center">
                    <Users className="w-4 h-4 text-blue-600" />
                  </div>
                </div>
                <div className="ml-4">
                  <p className="text-sm font-medium text-gray-600">Unieke Gebruikers</p>
                  <p className="text-2xl font-bold text-gray-900">{stats?.uniqueUsers || 0}</p>
                </div>
              </div>
            </CardContent>
          </Card>
          <Card className="border border-gray-200">
            <CardContent className="p-6">
              <div className="flex items-center">
                <div className="flex-shrink-0">
                  <div className="w-8 h-8 bg-red-100 rounded-lg flex items-center justify-center">
                    <AlertTriangle className="w-4 h-4 text-red-600" />
                  </div>
                </div>
                <div className="ml-4">
                  <p className="text-sm font-medium text-gray-600">Phishing Pogingen</p>
                  <p className="text-2xl font-bold text-gray-900">{phishedUsers}</p>
                </div>
              </div>
            </CardContent>
          </Card>
          <Card className="border border-gray-200">
            <CardContent className="p-6">
              <div className="flex items-center">
                <div className="flex-shrink-0">
                  <div className="w-8 h-8 bg-green-100 rounded-lg flex items-center justify-center">
                    <Shield className="w-4 h-4 text-green-600" />
                  </div>
                </div>
                <div className="ml-4">
                  <p className="text-sm font-medium text-gray-600">Training Voltooid</p>
                  <p className="text-2xl font-bold text-gray-900">{completedEducation}</p>
                </div>
              </div>
            </CardContent>
          </Card>
          <Card className="border border-gray-200">
            <CardContent className="p-6">
              <div className="flex items-center">
                <div className="flex-shrink-0">
                  <div className="w-8 h-8 bg-yellow-100 rounded-lg flex items-center justify-center">
                    <Percent className="w-4 h-4 text-yellow-600" />
                  </div>
                </div>
                <div className="ml-4">
                  <p className="text-sm font-medium text-gray-600">Training Percent</p>
                  <p className="text-2xl font-bold text-gray-900">{successRate}%</p>
                </div>
              </div>
            </CardContent>
          </Card>
        </div>
        {/* Training Sessions Table */}
        <Card className="border border-gray-200">
          <CardHeader className="px-6 py-4 border-b border-gray-200">
            <div className="flex items-center justify-between">
              <CardTitle className="text-lg font-medium text-gray-900">Training Sessies</CardTitle>
              <div className="flex items-center space-x-3">
                <Button 
                  onClick={exportData}
                  className="bg-green-600 text-white hover:bg-green-700 transition-colors"
                  size="sm"
                >
                  <Download className="w-4 h-4 mr-2" />
                  Exporteren
                </Button>
              </div>
            </div>
          </CardHeader>
          <CardContent className="p-0">
            <div className="overflow-x-auto">
              <Table>
                <TableHeader>
                  <TableRow>
                    <TableHead className="px-6 py-3">Gebruiker</TableHead>
                    <TableHead className="px-6 py-3">Tijdstip</TableHead>
                    <TableHead className="px-6 py-3">Status</TableHead>
                    <TableHead className="px-6 py-3">IP Adres</TableHead>
                    <TableHead className="px-6 py-3">Training Voltooid</TableHead>
                    <TableHead className="px-6 py-3">User Agent</TableHead>
                  </TableRow>
                </TableHeader>
                <TableBody>
                  {attempts?.map((attempt) => (
                    <TableRow key={attempt.id}>
                      <TableCell className="px-6 py-4">
                        <div className="flex items-center">
                          <div className="flex-shrink-0 h-10 w-10">
                            <div className="h-10 w-10 rounded-full bg-gray-300 flex items-center justify-center">
                              <span className="text-gray-600 font-medium text-sm">
                                {attempt.username.substring(0, 2).toUpperCase()}
                              </span>
                            </div>
                          </div>
                          <div className="ml-4">
                            <div className="text-sm font-medium text-gray-900">{attempt.username}</div>
                          </div>
                        </div>
                      </TableCell>
                      <TableCell className="px-6 py-4 text-sm text-gray-900">
                        {format(new Date(attempt.timestamp), "dd MMM yyyy HH:mm", { locale: nl })}
                      </TableCell>
                      <TableCell className="px-6 py-4">
                        <Badge variant="destructive">
                          <AlertTriangle className="w-3 h-3 mr-1" />
                          Gevallen voor phishing
                        </Badge>
                      </TableCell>
                      <TableCell className="px-6 py-4 text-sm text-gray-900">
                        {attempt.ipAddress || "Onbekend"}
                      </TableCell>
                      <TableCell className="px-6 py-4">
                        {attempt.completedEducation ? (
                          <Badge variant="secondary" className="bg-green-100 text-green-800">
                            <Shield className="w-3 h-3 mr-1" />
                            Ja
                          </Badge>
                        ) : (
                          <Badge variant="outline">
                            Nee
                          </Badge>
                        )}
                      </TableCell>
                      <TableCell className="px-6 py-4 text-sm text-gray-500 max-w-xs truncate">
                        {attempt.userAgent || "Onbekend"}
                      </TableCell>
                    </TableRow>
                  ))}
                  {!attempts?.length && (
                    <TableRow>
                      <TableCell colSpan={6} className="px-6 py-8 text-center text-gray-500">
                        Nog geen training pogingen geregistreerd.
                      </TableCell>
                    </TableRow>
                  )}
                </TableBody>
              </Table>
            </div>
          </CardContent>
        </Card>
      </div>
    </div>
  );
}
client/src/components/educational-modal.tsx
import { Dialog, DialogContent, DialogHeader, DialogTitle } from "@/components/ui/dialog";
import { Button } from "@/components/ui/button";
import { AlertTriangle, Info, CheckCircle, GraduationCap, X } from "lucide-react";
interface EducationalModalProps {
  isOpen: boolean;
  onClose: () => void;
  onEducationComplete: () => void;
}
export default function EducationalModal({ isOpen, onClose, onEducationComplete }: EducationalModalProps) {
  return (
    <Dialog open={isOpen} onOpenChange={onClose}>
      <DialogContent className="max-w-2xl">
        <DialogHeader>
          <div className="text-center mb-6">
            <div className="mx-auto flex items-center justify-center h-16 w-16 rounded-full bg-yellow-100 mb-4">
              <AlertTriangle className="text-yellow-600 w-8 h-8" />
            </div>
            <DialogTitle className="text-2xl font-bold text-gray-900 mb-2">
              Oeps! Je bent gevallen voor een phishing simulatie
            </DialogTitle>
            <p className="text-gray-600">Maar geen zorgen - dit is een veilige leeromgeving!</p>
          </div>
        </DialogHeader>
        <div className="space-y-6">
          <div className="bg-red-50 border-l-4 border-red-400 p-4">
            <div className="flex">
              <div className="flex-shrink-0">
                <Info className="text-red-400 w-5 h-5" />
              </div>
              <div className="ml-3">
                <h4 className="text-sm font-medium text-red-800">Wat er gebeurde:</h4>
                <p className="mt-1 text-sm text-red-700">
                  Je hebt zojuist je inloggegevens ingevoerd op een nepversie van Magister. 
                  In een echte phishing aanval zouden je gegevens nu gestolen zijn.
                </p>
              </div>
            </div>
          </div>
          <div className="bg-blue-50 border-l-4 border-blue-400 p-4">
            <div className="flex">
              <div className="flex-shrink-0">
                <Info className="text-blue-400 w-5 h-5" />
              </div>
              <div className="ml-3">
                <h4 className="text-sm font-medium text-blue-800">Waarop je had kunnen letten:</h4>
                <ul className="mt-2 text-sm text-blue-700 space-y-1">
                  <li>• Controleer altijd de URL in je adresbalk</li>
                  <li>• Let op spelfouten en verdachte domeinnamen</li>
                  <li>• Wees voorzichtig met links in e-mails</li>
                  <li>• Gebruik tweefactorauthenticatie waar mogelijk</li>
                  <li>• Verifieer altijd de identiteit van de afzender</li>
                </ul>
              </div>
            </div>
          </div>
          <div className="bg-green-50 border-l-4 border-green-400 p-4">
            <div className="flex">
              <div className="flex-shrink-0">
                <CheckCircle className="text-green-400 w-5 h-5" />
              </div>
              <div className="ml-3">
                <h4 className="text-sm font-medium text-green-800">Je privacy is beschermd:</h4>
                <p className="mt-1 text-sm text-green-700">
                  Je echte wachtwoord is veilig. Deze training gebruikt geen echte inloggegevens 
                  en alle data wordt alleen voor educatieve doeleinden gebruikt.
                </p>
              </div>
            </div>
          </div>
        </div>
        <div className="mt-8 flex flex-col sm:flex-row gap-3">
          <Button 
            onClick={onEducationComplete}
            className="flex-1 bg-blue-600 text-white hover:bg-blue-700 transition-colors"
          >
            <GraduationCap className="w-4 h-4 mr-2" />
            Training Voltooid
          </Button>
          <Button 
            onClick={onClose}
            variant="outline"
            className="flex-1"
          >
            <X className="w-4 h-4 mr-2" />
            Sluiten
          </Button>
        </div>
        <div className="mt-4 text-center text-sm text-gray-500">
          <p>Vragen over deze training? Neem contact op met je IT-afdeling.</p>
        </div>
      </DialogContent>
    </Dialog>
  );
}
client/src/lib/queryClient.ts
import { QueryClient } from "@tanstack/react-query";
async function throwIfResNotOk(res: Response) {
  if (!res.ok) {
    const body = await res.text();
    throw new Error(`${res.status} ${res.statusText}: ${body}`);
  }
}
export async function apiRequest(
  method: "GET" | "POST" | "PUT" | "DELETE" | "PATCH",
  path: string,
  body?: Record<string, any>
): Promise<Response> {
  const requestInit: RequestInit = {
    method,
    headers: {
      "Content-Type": "application/json",
    },
  };
  if (body) {
    requestInit.body = JSON.stringify(body);
  }
  const res = await fetch(path, requestInit);
  await throwIfResNotOk(res);
  return res;
}
type UnauthorizedBehavior = "returnNull" | "throw";
export const getQueryFn: <T>(options: {
  on401: UnauthorizedBehavior;
}) => ({ queryKey }: { queryKey: readonly any[] }) => Promise<T> =
  ({ on401 }) =>
  ({ queryKey }) => {
    const path = queryKey[0] as string;
    return apiRequest("GET", path).then(async (res) => {
      if (res.status === 401) {
        if (on401 === "returnNull") {
          return null;
        } else {
          throw new Error("Unauthorized");
        }
      }
      return res.json();
    });
  };
export const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      queryFn: getQueryFn({ on401: "throw" }),
    },
  },
});    queryKey: ["/api/admin/stats"],
  });
  const { data: attempts, isLoading: attemptsLoading } = useQuery<TrainingAttempt[]>({
    queryKey: ["/api/admin/attempts"],
  });
  const exportData = () => {
    if (!attempts) return;
    
    const csvContent = [
      ["Gebruikersnaam", "Tijdstip", "IP Adres", "User Agent", "Training Voltooid"].join(","),
      ...attempts.map(attempt => [
        attempt.username,
        format(new Date(attempt.timestamp), "yyyy-MM-dd HH:mm:ss"),
        attempt.ipAddress || "Onbekend",
        attempt.userAgent || "Onbekend",
        attempt.completedEducation ? "Ja" : "Nee"
      ].join(","))
    ].join("\n");
    const blob = new Blob([csvContent], { type: "text/csv;charset=utf-8;" });
    const link = document.createElement("a");
    const url = URL.createObjectURL(blob);
    link.setAttribute("href", url);
    link.setAttribute("download", `phishing-training-${format(new Date(), "yyyy-MM-dd")}.csv`);
    link.style.visibility = "hidden";
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
  };
  if (statsLoading || attemptsLoading) {
    return (
      <div className="min-h-screen bg-gray-50 p-6">
        <div className="max-w-7xl mx-auto">
          <div className="animate-pulse">
            <div className="h-8 bg-gray-200 rounded w-1/4 mb-4"></div>
            <div className="grid grid-cols-1 md:grid-cols-4 gap-6 mb-8">
              {[...Array(4)].map((_, i) => (
                <div key={i} className="bg-white rounded-lg shadow-sm p-6 border border-gray-200">
                  <div className="h-4 bg-gray-200 rounded w-3/4 mb-2"></div>
                  <div className="h-8 bg-gray-200 rounded w-1/2"></div>
                </div>
              ))}
            </div>
          </div>
        </div>
      </div>
    );
  }
  const phishedUsers = attempts?.length || 0;
  const completedEducation = attempts?.filter(a => a.completedEducation).length || 0;
  const successRate = phishedUsers > 0 ? Math.round((completedEducation / phishedUsers) * 100) : 0;
  return (
    <div className="min-h-screen bg-gray-50 p-6">
      <div className="max-w-7xl mx-auto">
        <div className="mb-8">
          <h1 className="text-2xl font-bold text-gray-900 mb-2">Training Dashboard</h1>
          <p className="text-gray-600">Overzicht van phishing training sessies en resultaten</p>
        </div>
        {/* Stats Overview */}
        <div className="grid grid-cols-1 md:grid-cols-4 gap-6 mb-8">
          <Card className="border border-gray-200">
            <CardContent className="p-6">
              <div className="flex items-center">
                <div className="flex-shrink-0">
                  <div className="w-8 h-8 bg-blue-100 rounded-lg flex items-center justify-center">
                    <Users className="w-4 h-4 text-blue-600" />
                  </div>
                </div>
                <div className="ml-4">
                  <p className="text-sm font-medium text-gray-600">Unieke Gebruikers</p>
                  <p className="text-2xl font-bold text-gray-900">{stats?.uniqueUsers || 0}</p>
                </div>
              </div>
            </CardContent>
          </Card>
          <Card className="border border-gray-200">
            <CardContent className="p-6">
              <div className="flex items-center">
                <div className="flex-shrink-0">
                  <div className="w-8 h-8 bg-red-100 rounded-lg flex items-center justify-center">
                    <AlertTriangle className="w-4 h-4 text-red-600" />
                  </div>
                </div>
                <div className="ml-4">
                  <p className="text-sm font-medium text-gray-600">Phishing Pogingen</p>
                  <p className="text-2xl font-bold text-gray-900">{phishedUsers}</p>
                </div>
              </div>
            </CardContent>
          </Card>
          <Card className="border border-gray-200">
            <CardContent className="p-6">
              <div className="flex items-center">
                <div className="flex-shrink-0">
                  <div className="w-8 h-8 bg-green-100 rounded-lg flex items-center justify-center">
                    <Shield className="w-4 h-4 text-green-600" />
                  </div>
                </div>
                <div className="ml-4">
                  <p className="text-sm font-medium text-gray-600">Training Voltooid</p>
                  <p className="text-2xl font-bold text-gray-900">{completedEducation}</p>
                </div>
              </div>
            </CardContent>
          </Card>
          <Card className="border border-gray-200">
            <CardContent className="p-6">
              <div className="flex items-center">
                <div className="flex-shrink-0">
                  <div className="w-8 h-8 bg-yellow-100 rounded-lg flex items-center justify-center">
                    <Percent className="w-4 h-4 text-yellow-600" />
                  </div>
                </div>
                <div className="ml-4">
                  <p className="text-sm font-medium text-gray-600">Training Percent</p>
                  <p className="text-2xl font-bold text-gray-900">{successRate}%</p>
                </div>
              </div>
            </CardContent>
          </Card>
        </div>
        {/* Training Sessions Table */}
        <Card className="border border-gray-200">
          <CardHeader className="px-6 py-4 border-b border-gray-200">
            <div className="flex items-center justify-between">
              <CardTitle className="text-lg font-medium text-gray-900">Training Sessies</CardTitle>
              <div className="flex items-center space-x-3">
                <Button 
                  onClick={exportData}
                  className="bg-green-600 text-white hover:bg-green-700 transition-colors"
                  size="sm"
                >
                  <Download className="w-4 h-4 mr-2" />
                  Exporteren
                </Button>
              </div>
            </div>
          </CardHeader>
          <CardContent className="p-0">
            <div className="overflow-x-auto">
              <Table>
                <TableHeader>
                  <TableRow>
                    <TableHead className="px-6 py-3">Gebruiker</TableHead>
                    <TableHead className="px-6 py-3">Tijdstip</TableHead>
                    <TableHead className="px-6 py-3">Status</TableHead>
                    <TableHead className="px-6 py-3">IP Adres</TableHead>
                    <TableHead className="px-6 py-3">Training Voltooid</TableHead>
                    <TableHead className="px-6 py-3">User Agent</TableHead>
                  </TableRow>
                </TableHeader>
                <TableBody>
                  {attempts?.map((attempt) => (
                    <TableRow key={attempt.id}>
                      <TableCell className="px-6 py-4">
                        <div className="flex items-center">
                          <div className="flex-shrink-0 h-10 w-10">
                            <div className="h-10 w-10 rounded-full bg-gray-300 flex items-center justify-center">
                              <span className="text-gray-600 font-medium text-sm">
                                {attempt.username.substring(0, 2).toUpperCase()}
                              </span>
                            </div>
                          </div>
                          <div className="ml-4">
                            <div className="text-sm font-medium text-gray-900">{attempt.username}</div>
                          </div>
                        </div>
                      </TableCell>
                      <TableCell className="px-6 py-4 text-sm text-gray-900">
                        {format(new Date(attempt.timestamp), "dd MMM yyyy HH:mm", { locale: nl })}
                      </TableCell>
                      <TableCell className="px-6 py-4">
                        <Badge variant="destructive">
                          <AlertTriangle className="w-3 h-3 mr-1" />
                          Gevallen voor phishing
                        </Badge>
                      </TableCell>
                      <TableCell className="px-6 py-4 text-sm text-gray-900">
                        {attempt.ipAddress || "Onbekend"}
                      </TableCell>
                      <TableCell className="px-6 py-4">
                        {attempt.completedEducation ? (
                          <Badge variant="secondary" className="bg-green-100 text-green-800">
                            <Shield className="w-3 h-3 mr-1" />
                            Ja
                          </Badge>
                        ) : (
                          <Badge variant="outline">
                            Nee
                          </Badge>
                        )}
                      </TableCell>
                      <TableCell className="px-6 py-4 text-sm text-gray-500 max-w-xs truncate">
                        {attempt.userAgent || "Onbekend"}
                      </TableCell>
                    </TableRow>
                  ))}
                  {!attempts?.length && (
                    <TableRow>
                      <TableCell colSpan={6} className="px-6 py-8 text-center text-gray-500">
                        Nog geen training pogingen geregistreerd.
                      </TableCell>
                    </TableRow>
                  )}
                </TableBody>
              </Table>
            </div>
          </CardContent>
        </Card>
      </div>
    </div>
  );
}
client/src/components/educational-modal.tsx
import { Dialog, DialogContent, DialogHeader, DialogTitle } from "@/components/ui/dialog";
import { Button } from "@/components/ui/button";
import { AlertTriangle, Info, CheckCircle, GraduationCap, X } from "lucide-react";
interface EducationalModalProps {
  isOpen: boolean;
  onClose: () => void;
  onEducationComplete: () => void;
}
export default function EducationalModal({ isOpen, onClose, onEducationComplete }: EducationalModalProps) {
  return (
    <Dialog open={isOpen} onOpenChange={onClose}>
      <DialogContent className="max-w-2xl">
        <DialogHeader>
          <div className="text-center mb-6">
            <div className="mx-auto flex items-center justify-center h-16 w-16 rounded-full bg-yellow-100 mb-4">
              <AlertTriangle className="text-yellow-600 w-8 h-8" />
            </div>
            <DialogTitle className="text-2xl font-bold text-gray-900 mb-2">
              Oeps! Je bent gevallen voor een phishing simulatie
            </DialogTitle>
            <p className="text-gray-600">Maar geen zorgen - dit is een veilige leeromgeving!</p>
          </div>
        </DialogHeader>
        <div className="space-y-6">
          <div className="bg-red-50 border-l-4 border-red-400 p-4">
            <div className="flex">
              <div className="flex-shrink-0">
                <Info className="text-red-400 w-5 h-5" />
              </div>
              <div className="ml-3">
                <h4 className="text-sm font-medium text-red-800">Wat er gebeurde:</h4>
                <p className="mt-1 text-sm text-red-700">
                  Je hebt zojuist je inloggegevens ingevoerd op een nepversie van Magister. 
                  In een echte phishing aanval zouden je gegevens nu gestolen zijn.
                </p>
              </div>
            </div>
          </div>
          <div className="bg-blue-50 border-l-4 border-blue-400 p-4">
            <div className="flex">
              <div className="flex-shrink-0">
                <Info className="text-blue-400 w-5 h-5" />
              </div>
              <div className="ml-3">
                <h4 className="text-sm font-medium text-blue-800">Waarop je had kunnen letten:</h4>
                <ul className="mt-2 text-sm text-blue-700 space-y-1">
                  <li>• Controleer altijd de URL in je adresbalk</li>
                  <li>• Let op spelfouten en verdachte domeinnamen</li>
                  <li>• Wees voorzichtig met links in e-mails</li>
                  <li>• Gebruik tweefactorauthenticatie waar mogelijk</li>
                  <li>• Verifieer altijd de identiteit van de afzender</li>
                </ul>
              </div>
            </div>
          </div>
          <div className="bg-green-50 border-l-4 border-green-400 p-4">
            <div className="flex">
              <div className="flex-shrink-0">
                <CheckCircle className="text-green-400 w-5 h-5" />
              </div>
              <div className="ml-3">
                <h4 className="text-sm font-medium text-green-800">Je privacy is beschermd:</h4>
                <p className="mt-1 text-sm text-green-700">
                  Je echte wachtwoord is veilig. Deze training gebruikt geen echte inloggegevens 
                  en alle data wordt alleen voor educatieve doeleinden gebruikt.
                </p>
              </div>
            </div>
          </div>
        </div>
        <div className="mt-8 flex flex-col sm:flex-row gap-3">
          <Button 
            onClick={onEducationComplete}
            className="flex-1 bg-blue-600 text-white hover:bg-blue-700 transition-colors"
          >
            <GraduationCap className="w-4 h-4 mr-2" />
            Training Voltooid
          </Button>
          <Button 
            onClick={onClose}
            variant="outline"
            className="flex-1"
          >
            <X className="w-4 h-4 mr-2" />
            Sluiten
          </Button>
        </div>
        <div className="mt-4 text-center text-sm text-gray-500">
          <p>Vragen over deze training? Neem contact op met je IT-afdeling.</p>
        </div>
      </DialogContent>
    </Dialog>
  );
}
client/src/lib/queryClient.ts
import { QueryClient } from "@tanstack/react-query";
async function throwIfResNotOk(res: Response) {
  if (!res.ok) {
    const body = await res.text();
    throw new Error(`${res.status} ${res.statusText}: ${body}`);
  }
}
export async function apiRequest(
  method: "GET" | "POST" | "PUT" | "DELETE" | "PATCH",
  path: string,
  body?: Record<string, any>
): Promise<Response> {
  const requestInit: RequestInit = {
    method,
    headers: {
      "Content-Type": "application/json",
    },
  };
  if (body) {
    requestInit.body = JSON.stringify(body);
  }
  const res = await fetch(path, requestInit);
  await throwIfResNotOk(res);
  return res;
}
type UnauthorizedBehavior = "returnNull" | "throw";
export const getQueryFn: <T>(options: {
  on401: UnauthorizedBehavior;
}) => ({ queryKey }: { queryKey: readonly any[] }) => Promise<T> =
  ({ on401 }) =>
  ({ queryKey }) => {
    const path = queryKey[0] as string;
    return apiRequest("GET", path).then(async (res) => {
      if (res.status === 401) {
        if (on401 === "returnNull") {
          return null;
        } else {
          throw new Error("Unauthorized");
        }
      }
      return res.json();
    });
  };
export const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      queryFn: getQueryFn({ on401: "throw" }),
    },
  },
});
